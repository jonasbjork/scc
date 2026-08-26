# Övning: Privata och publika subnät med Docker-nätverk

I molnet delar vi ofta upp våra resurser i privata och publika subnät. Ett
publikt subnät har en väg ut till internet, medan ett privat subnät saknar
den vägen. Trafik därifrån kommer helt enkelt ingenstans, om vi inte
själva bygger en *NAT-gateway* (_Network Address Translation_) som släpper
ut den.

Vi behöver inget molnkonto för att förstå principen. Med Docker kan vi
återskapa exakt samma beteende lokalt på våra egna datorer och i den här
övningen bygger vi det själva, steg för steg.

## Mål med övningen

När vi är klara ska vi:

- kunna skapa ett Docker-nätverk som saknar väg ut till internet
- förstå vad flaggan `--internal` gör och varför den motsvarar ett privat
  subnät utan NAT-gateway
- kunna testa och förklara skillnaden i uppkoppling mellan en container i
  ett privat och en container i ett publikt nätverk

## Förkunskaper

Vi behöver ha Docker installerat och kunna öppna en terminal. Det spelar
ingen roll om vi kör Fedora, RHEL, CentOS Stream, Windows eller macOS. Kommandona
är desamma.

## Steg 1: Skapa nätverken

Vi börjar med att skapa två nätverk: ett privat och ett publikt.

```console
docker network create --internal privat-natverk
docker network create publikt-natverk
```

`--internal`
: skapar ett nätverk helt utan väg ut till internet. Docker sätter inte
  upp någon NAT-regel för trafik som lämnar nätverket, vilket motsvarar
  ett privat subnät utan NAT-gateway i molnet.

Nätverket `publikt-natverk` skapas utan `--internal` och får därför den
vanliga vägen ut, precis som ett publikt subnät med en internetgateway.

## Steg 2: Starta en container i respektive nätverk

Nu startar vi en container i vardera nätverket.

```console
docker run -dit --name databas --network privat-natverk fedora:44 bash
docker run -dit --name webb --network publikt-natverk fedora:44 bash
```

`-d`
: kör containern i bakgrunden (_detached_).

`-i`
: håller standard in öppen, så att vi kan skicka in kommandon till
  containern.

`-t`
: allokerar en pseudo-terminal, vilket gör containern interaktiv.

`--name`
: ger containern ett namn vi kan referera till, istället för det
  slumpmässigt genererade ID som Docker annars ger den.

`--network`
: bestämmer vilket av våra två nätverk containern ansluts till.

## Steg 3: Testa uppkopplingen mot internet

Vi testar nu om respektive container når ut till internet.

```console
docker exec webb curl -sI https://example.com
docker exec databas curl -sI https://example.com
```

`-s`
: tyst läge (_silent_) - vi slipper se förloppsindikatorn.

`-I`
: hämtar bara HTTP-huvudena, inte hela sidan, vilket räcker för att
  visa att vi når fram.

`webb`-containern ska få ett svar med HTTP-huvuden tillbaka.
`databas`-containern ska antingen hänga sig eller till slut tidsöka ut,
eftersom nätverket saknar väg ut. Precis som ett privat subnät utan
NAT-gateway.

> Om `databas`-anropet hänger sig i flera sekunder är det förväntat. Det
> finns ingen väg ut ur nätverket, så anropet väntar till det slutligen
> når en time-out. Vi kan avbryta med `Ctrl+C` om vi inte vill vänta.
>
> Lägg märke till att det inte spelar någon roll vilket kommando vi
> skriver inifrån containern, det finns helt enkelt ingen väg ut.

## Steg 4: Städa upp efter oss

När vi är klara tar vi bort containrarna och nätverken igen.

```console
docker rm -f webb databas
docker network rm privat-natverk publikt-natverk
```

`docker rm -f`
: stoppar och tar bort containrarna i ett och samma steg, även om de
  fortfarande körs.

`docker network rm`
: tar bort nätverken när ingen container längre använder dem.

## Reflektionsfrågor

1. Varför kunde `webb`-containern nå internet, men inte `databas`?
2. Vad motsvarar flaggan `--internal` om vi jämför med ett moln som AWS
   eller Azure?
3. Om vi ville att `databas` skulle kunna hämta säkerhetsuppdateringar
   från internet, men fortfarande vara helt onåbar utifrån. Vad hade vi
   behövt lägga till?
4. Testa att ansluta `databas` till det publika nätverket också, med
   `docker network connect publikt-natverk databas` och kör om testet
   från steg 3. Vad händer, och varför?

> **Tips inför fråga 3:** tänk på vad en NAT-gateway faktiskt gör i
> molnet. Den släpper ut trafik, men släpper inte in någon trafik som inte initierats innefrån.
