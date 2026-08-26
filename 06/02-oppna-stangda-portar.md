# Övning: Publicerade portar med Docker - motsvarigheten till en brandväggsregel

I den förra övningen tittade vi på hur ett helt nätverk kan vara privat
eller publikt. Nu zoomar vi in på en enskild container och tittar på
samma princip på portnivå: en publicerad port fungerar som en öppen
brandväggsregel in mot containern, medan en container utan publicerad
port är helt onåbar utifrån. Oavsett vad som faktiskt körs där inne.

## Mål med övningen

När vi är klara ska vi:

- kunna publicera en containers port mot värden med `-p`
- förstå att en container utan publicerad port är onåbar utifrån, även
  om tjänsten inuti fungerar perfekt
- kunna förklara skillnaden mellan att en anslutning nekas direkt och
  att den tidsöker ut

## Förkunskaper

Vi behöver Docker installerat. Det är en fördel, men inget krav, att vi
redan gjort övningen om privata och publika nätverk, eftersom vi bygger
vidare på samma tankesätt här.

## Steg 1: Starta två containrar - en öppen, en stängd

```console
docker run -d --name test-oppen -p 8080:80 python:3.14 python3 -m http.server 80
docker run -d --name test-stangd python:3.14 python3 -m http.server 80
```

`-d`
: kör containern i bakgrunden (_detached_).

`--name`
: ger containern ett namn vi kan referera till.

`-p 8080:80`
: publicerar containerns port 80 på värdens port 8080. Motsvarande att
  öppna en brandväggsregel mot omvärlden. Utan den här flaggan finns det
  ingen väg in till containern utifrån, oavsett vad som körs där inne.

`python3 -m http.server 80`
: startar Pythons inbyggda webbserver på port 80 inuti containern. Vi
  behöver inte installera något extra för det, modulen finns i 
  Python.

Lägg märke till att `test-stangd` startas exakt likadant, förutom att
`-p 8080:80` saknas helt.

## Steg 2: Testa åtkomst från värdmaskinen

```console
curl -sI http://localhost:8080
curl -sI http://localhost:8081
```

Det första anropet ska ge oss ett svar med HTTP-huvuden tillbaka. Det
andra ska misslyckas, eftersom `test-stangd` aldrig fick någon
publicerad port. Det finns helt enkelt ingen tjänst att nå på 8081,
motsvarande en resurs helt utan publik exponering.

> Notera skillnaden mot förra övningen. Där fick anropet en time-out, eftersom
> nätverket saknade väg *ut*. Här nekas anropet direkt (`Connection
> refused`), eftersom det inte finns någon väg *in* alls. Ingen lyssnar
> på 8081. Båda är former av avgränsning, men de sker på olika ställen
> och syns olika för den som testar.

## Steg 3: Städa upp efter oss

```console
docker rm -f test-oppen test-stangd
```

## Reflektionsfrågor

1. Varför fick vi svar på port 8080, men inte på 8081?
2. Vad är skillnaden mellan att en anslutning nekas direkt och att den
   tidsöker ut? Vad säger det om var avgränsningen sker i respektive
   fall?
3. Testa att starta om `test-stangd` med `-p 127.0.0.1:8082:80` i
   stället för att helt sakna `-p`. Vad förändras jämfört med `8080`, och
   vad tror vi att `127.0.0.1` gör i den här flaggan?
4. Använd `docker inspect test-stangd` för att hitta containerns interna
   IP-adress, och prova att nå den direkt från värden trots att ingen
   port är publicerad. Fungerade det? Fundera på vad det säger om
   skillnaden mellan en *publik* och en *privat* IP-adress i molnet.

> **Not:** fråga 4 kan bete sig olika beroende på om vi kör Docker
> direkt på Linux eller via Docker Desktop på Windows eller macOS, där containrarna i
> praktiken körs inuti en virtuell maskin. Det är själva poängen med
> frågan. Jämför gärna resultatet mellan olika miljöer i klassen.
