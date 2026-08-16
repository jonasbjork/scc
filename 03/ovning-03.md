# Övning - Rollbaserad åtkomstkontroll i PostgreSQL

I den här övningen kommer vi:

- skapa roller i en PostgreSQL-databas
- koppla behörigheter till roller i stället för till enskilda användare
- lägga till och ta bort användare från roller och se effekten direkt

Det här är RBAC i praktiken, den modell de flesta stöter på först. Vi ser konkret hur behörigheter kan förvaltas genom roller.

## Starta databasen

```console
docker run -d --name rbac-db -e POSTGRES_PASSWORD=hemligt postgres:17
```

Vi kopplar upp oss mot databasen som administratör:

```console
docker exec -it rbac-db psql -U postgres
```

Nu är vi inne i `psql`, PostgreSQLs kommandorad. Alla kommandon nedan skriver vi där.

## Bygg upp en liten miljö

Vi skapar en tabell med lite data att skydda:

```console
CREATE TABLE loner (namn text, belopp int);
INSERT INTO loner VALUES ('Anna', 45000), ('Bo', 38000);
```

Nu skapar vi två *grupproller*, alltså roller som samlar behörigheter men som ingen loggar in med direkt:

```console
CREATE ROLE lasare;
CREATE ROLE skrivare;
```

Vi ger rollerna varsin uppsättning behörigheter:

```console
GRANT SELECT ON loner TO lasare;
GRANT SELECT, INSERT, UPDATE ON loner TO skrivare;
```

`GRANT SELECT`
: rätt att läsa ur tabellen.

`INSERT, UPDATE`
: rätt att lägga till och ändra rader.

Nu skapar vi två personer och kopplar dem till varsin roll:

```console
CREATE ROLE anna LOGIN PASSWORD 'annas-losen';
CREATE ROLE bo LOGIN PASSWORD 'bos-losen';
GRANT lasare TO anna;
GRANT skrivare TO bo;
```

`LOGIN`
: gör att rollen kan användas för att logga in, alltså en riktig användare.

`GRANT lasare TO anna`
: ger Anna allt som rollen *lasare* har rätt till - varken mer eller mindre.

## Prova behörigheterna

Vi lämnar `psql` med `\q` och loggar in som Anna:

```console
docker exec -it rbac-db psql "postgresql://anna:annas-losen@127.0.0.1:5432/postgres"
```

Anna får läsa:

```console
SELECT * FROM loner;
```

Men hon får inte skriva:

```console
INSERT INTO loner VALUES ('Cecilia', 41000);
```

Anna nekas. Bo, som har rollen *skrivare*, skulle klara samma kommando.

Det fina är att vi nu kan ändra Annas åtkomst utan att röra hennes konto. Som administratör kan vi flytta henne till skrivare-rollen, och hon får omedelbart de rättigheterna. Vi behöver aldrig peta i enskilda behörigheter per person.

Låt oss också prova med *bo*. Logga ut från `psql` genom att skriva `\q`.

Sedan loggar vi in som Bo:

```console
docker exec -it rbac-db psql "postgresql://bo:bos-losen@127.0.0.1:5432/postgres"
```

Bo får läsa:

```console
SELECT * FROM loner;
```

Bo får också skriva:

```console
INSERT INTO loner VALUES ('Cecilia', 41000);
```

Bo kan läsa och se den nya lönen:

```console
SELECT * FROM loner;
```

## Reflektion och redovisning

- Vad är vinsten med att lägga behörigheter på rollerna *lasare* och *skrivare* i stället för direkt på Anna och Bo?
- Om tio nya utvecklare börjar samma dag, hur mycket arbete krävs för att ge dem rätt åtkomst med den här modellen?
- Materialet nämner att även läsrättigheter kan vara känsliga. Stämmer det på vår lonetabell?
