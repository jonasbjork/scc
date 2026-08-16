
# Övning - Kortlivade credentials med Vault

I den här övningen kommer vi:

- starta *HashiCorp Vault* och en PostgreSQL-databas i varsin container
- låta Vault skapa databasinloggningar som automatiskt går ut efter en kort stund
- se med egna ögon vad skillnaden mellan en permanent och en kortlivad *credential* innebär för en angripare

Materialet tar upp att långlivade credentials är som ett lås vars nyckel aldrig byts ut. Nu ska vi bygga motsatsen: en nyckel som byter sig själv med jämna mellanrum.

### Förberedelser

Först skapar vi ett eget nätverk så att våra två containrar kan prata med varandra:

```console
docker network create iamnet
```

`network create`
: skapar ett isolerat Dockernätverk.

`iamnet`
: namnet vi ger nätverket. Vi använder det i nästa två kommandon.

Sedan startar vi databasen:

```console
docker run -d --name postgres --network iamnet -e POSTGRES_PASSWORD=hemligt postgres:17
```

`-d`
: kör containern i bakgrunden (*detached*).

`--name postgres`
: ger containern namnet *postgres*, vilket också blir dess värdnamn på nätverket.

`--network iamnet`
: ansluter containern till nätverket vi nyss skapade.

`-e POSTGRES_PASSWORD=hemligt`
: sätter lösenordet för databasens administratörskonto via en miljövariabel.

Nu startar vi Vault i så kallat *dev-läge*, ett förenklat läge som är tänkt just för att lära sig och prova:

```console
docker run -d --name vault --network iamnet --cap-add=IPC_LOCK -e VAULT_DEV_ROOT_TOKEN_ID=root -e VAULT_DEV_LISTEN_ADDRESS=0.0.0.0:8200 -p 8200:8200 hashicorp/vault
```

`--cap-add=IPC_LOCK`
: ger Vault rätt att låsa minne, vilket den vill göra för att skydda hemligheter.

`-e VAULT_DEV_ROOT_TOKEN_ID=root`
: sätter en känd *root-token* så att vi enkelt kan logga in i labben.

`-e VAULT_DEV_LISTEN_ADDRESS=0.0.0.0:8200`
: låter Vault lyssna på alla adresser inne i containern.

`-p 8200:8200`
: kopplar Vaults port till vår dator.

> Dev-läget håller allt i minnet och sätter en root-token som vi valt själva. Det är utmärkt för att lära sig, men det är också själva definitionen av osäkert. Vi använder det bara här i labben.

### Konfigurera Vault

Vi kliver in i Vault-containern och sätter några miljövariabler så att Vaults kommandorad vet var den ska ansluta:

```console
docker exec -it vault sh
```

Väl inne i containern:

```console
export VAULT_ADDR=http://127.0.0.1:8200
export VAULT_TOKEN=root
```

`VAULT_ADDR`
: adressen till Vault. Inifrån containern når vi den på localhost.

`VAULT_TOKEN`
: vår inloggning. Här använder vi root-token från tidigare.

Nu aktiverar vi Vaults *database secrets engine*, den funktion som kan skapa databasinloggningar åt oss:

```console
vault secrets enable database
```

Vi berättar för Vault hur den ansluter till vår Postgres:

```console
vault write database/config/postgres plugin_name=postgresql-database-plugin connection_url="postgresql://{{username}}:{{password}}@postgres:5432/postgres?sslmode=disable" allowed_roles="lasare" username="postgres" password="hemligt"
```

Det här kommandot säger åt Vault vilken databas den ska hantera, vilken roll (*lasare*) som får skapas mot den, och vilket konto Vault själv använder för att sköta jobbet.

> Här låter vi Vault ansluta som databasens administratör för att hålla labben kort. I en riktig miljö skulle vi ge Vault ett eget, snävt konto som *bara* får skapa och ta bort roller, precis enligt least privilege. Vaults eget konto ska inte kunna mer än det behöver.

Sedan skapar vi en roll där vi bestämmer hur länge en utdelad inloggning ska gälla:

```console
vault write database/roles/lasare db_name=postgres creation_statements="CREATE ROLE \"{{name}}\" WITH LOGIN PASSWORD '{{password}}' VALID UNTIL '{{expiration}}'; GRANT CONNECT ON DATABASE postgres TO \"{{name}}\";" default_ttl="2m" max_ttl="5m"
```

`default_ttl="2m"`
: en utdelad inloggning gäller i två minuter.

`max_ttl="5m"`
: den kan som mest förlängas till fem minuter.

`creation_statements`
: den SQL Vault kör för att skapa en tillfällig roll med ett slumpat namn och lösenord.

### Se det hända

Nu ber vi Vault om en färsk inloggning:

```console
vault read database/creds/lasare
```

Vi får tillbaka ett användarnamn och ett lösenord som Vault just skapade. Vi antecknar dem. Öppna gärna en andra terminal och testa att logga in på databasen med dem:

```console
docker exec -it postgres psql "postgresql://ANVANDARNAMN:LOSENORD@127.0.0.1:5432/postgres" -c "SELECT 1;"
```

Vi byter ut `ANVANDARNAMN` och `LOSENORD` mot värdena vi fick. Kommandot ska returnera en etta - inloggningen fungerar.

Nu väntar vi drygt två minuter och kör exakt samma kommando igen. Den här gången nekas vi. Vault har tagit bort rollen automatiskt, precis som avtalat.

### Reflektion och redovisning

- Vad hände med inloggningen efter två minuter, och varför är det bra ur ett säkerhetsperspektiv?
- Om en sådan här kortlivad token läcker ut, hur stort är angriparens tidsfönster jämfört med en permanent nyckel?
- Materialet skiljer på att *rotera* credentials och att använda *kortlivade* credentials. Vilket av dem gjorde vi här?
