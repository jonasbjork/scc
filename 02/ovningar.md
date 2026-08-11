# Secure Cloud Computing<br/>Övningar - Lektion 2


I den här delen ska vi arbeta praktiskt med de hot och risker vi läst om i kapitlet *Hot och risker i molnet*. Det fina är att vi inte behöver betala för någon molntjänst. Vi simulerar molnet lokalt på våra egna datorer med hjälp av containrar i Docker Desktop.

Allt vi gör här kan vi göra om hur många gånger vi vill, och vi kan riva miljön och börja om helt utan kostnad. Det är en av de stora fördelarna med att öva i containrar i stället för i en riktig molntjänst.

## Installera Docker Desktop

Vi behöver Docker Desktop installerat och igång. Det är programmet som låter oss köra containrar på vår egen dator.

1. Vi går till docker.com och laddar ner *Docker Desktop for Windows*.
2. Vi kör installationsfilen och följer guiden. Om installationen frågar om vi vill använda *WSL 2* svarar vi ja, det är det rekommenderade valet på Windows.
3. Efter installationen startar vi om datorn om vi blir ombedda.
4. Vi startar *Docker Desktop* från startmenyn. Första gången kan det ta en liten stund innan det är igång. Vi väntar tills valen i programmet slutar vara gråa och en liten val-ikon (en val med containrar på ryggen) visas nere i aktivitetsfältet.

> Notering för macOS: vi laddar i stället ner *Docker Desktop for Mac*. Väljer vi fel version fungerar det inte, så vi kontrollerar om vi har en Apple-processor (M1, M2, M3 och så vidare) eller en äldre Intel-processor och väljer därefter.

> Notering för Linux: på Linux kan vi antingen installera *Docker Desktop for Linux* eller bara *Docker Engine* via distributionens pakethanterare. Kör vi Fedora installerar vi det med `sudo dnf install docker-ce docker-ce-cli` efter att ha lagt till Dockers officiella paketförråd.

### Öppna en terminal

I nästan alla labbar skriver vi kommandon i en terminal. På Windows använder vi *PowerShell*.

Vi öppnar den genom att trycka på Windows-tangenten, skriva `powershell` och trycka Enter. Ett blått eller svart fönster öppnas där vi kan skriva kommandon.

> Notering för macOS och Linux: där använder vi programmet *Terminal* i stället. Kommandona är nästan alltid samma, men på ett par ställen skiljer sig sättet vi anger en mapp på. Där det spelar roll skriver vi ut skillnaden i en notering.

### Kontrollera att allt fungerar

Innan vi går vidare kontrollerar vi att Docker svarar. Vi skriver:

```console
docker --version
```

Vi ska då få tillbaka en rad som talar om vilken version av Docker vi har, till exempel `Docker version 27.x.x`. Får vi i stället ett felmeddelande om att kommandot inte hittas, eller att Docker inte kör, så kontrollerar vi att *Docker Desktop* verkligen är startat och att val-ikonen syns i aktivitetsfältet.

Vi provar sedan att köra vår första container:

```console
docker run --rm hello-world
```

`run`
: startar en ny container.

`--rm`
: talar om att containern ska tas bort automatiskt när den är klar, så att vi inte samlar på oss gamla containrar.

`hello-world`
: är namnet på den *image* vi vill köra. En image är en färdig mall som containern skapas från.

Fungerar allt får vi ett meddelande som börjar med "Hello from Docker!". Då är vi redo att börja.

### Några ord vi kommer att använda

Innan vi sätter igång går vi igenom fyra ord som återkommer i alla labbar.

- **Image** är en färdig, oföränderlig mall. Tänk på den som en ritning. Vi laddar ner images från internet, till exempel `minio/minio` eller `nginx`.
- **Container** är en levande instans som körs utifrån en image. Vi kan starta, stoppa och ta bort containrar utan att imagen påverkas. Från en image kan vi starta hur många containrar vi vill.
- **Port** är en dörr in till en container. När vi skriver `-p 9000:9000` säger vi att port 9000 på vår dator ska kopplas till port 9000 inne i containern, så att vi kan nå tjänsten via webbläsaren.
- **Volym** är en mapp på vår dator som vi delar in i containern, så att data inte försvinner när containern tas bort.

### En konvention vi använder i alla labbar

Kodblocken i det här materialet är skrivna för *PowerShell på Windows*. Där ett kommando behöver skrivas annorlunda på macOS eller Linux lägger vi in en notering direkt efter, ungefär så här:

> Notering för macOS och Linux: i stället för `${PWD}` skriver vi `$(pwd)`. Båda betyder samma sak, nämligen "den mapp jag står i just nu".

Vi kopierar aldrig med något dollartecken eller annat prompt-tecken i början av raden. Kodblocken innehåller bara själva kommandot, precis som vi ska skriva det.

### Städa upp efter oss

Containrar tar upp plats och använder minne. När vi är klara med en labb är det bra att städa upp. I varje labb finns en egen städsektion, men de vanligaste kommandona är:

```console
docker ps
```

Det visar alla containrar som körs just nu. Vill vi se även stoppade containrar lägger vi till `-a`:

```console
docker ps -a
```

För att stoppa och ta bort en container med ett visst namn skriver vi:

```console
docker stop namnet
docker rm namnet
```

> Vi ska vara försiktiga med kommandon som tar bort saker. `docker rm` tar bort en container, men om vi använt en volym för att spara data ligger den kvar tills vi tar bort även den. Vi tar aldrig bort en volym som innehåller något vi vill behålla.

Nu är vi redo. Vi börjar med labb 1.

## Labb 1 - Sårbarheter i images (Trivy)

### Vad vi ska lära oss

I den här labben ska vi:

- förstå att en *image* vi hämtar från internet kan innehålla kända sårbarheter
- använda verktyget *Trivy* för att skanna en image
- jämföra en gammal image med en nyare och se skillnaden
- förstå varför valet av basimage är en säkerhetsfråga

### Bakgrund

I kapitlet läste vi om *osäkra tredjepartsresurser*. När vi bygger våra egna tjänster i molnet utgår vi nästan alltid från färdiga byggstenar som andra har skapat, till exempel en image med Python eller en webbserver. Vi litar på att de är säkra, men det är inte alltid sant. En gammal image kan innehålla programvara med kända säkerhetshål och de hålen ärver vi rakt in i vår egen tjänst utan att vi gjort något fel själva.

Det verktyg vi ska använda heter *Trivy* och är ett populärt, gratis verktyg med öppen källkod. Det tittar inuti en image och jämför programvaran där mot en databas över kända sårbarheter.

### Förberedelser

Vi behöver *Docker Desktop* igång och en öppen *PowerShell*. Har vi inte gjort kom-igång-guiden ännu gör vi den först. Vi behöver inte ladda ner något extra, Trivy körs själv som en container.

### Steg för steg

#### Steg 1 - Skanna en äldre image

Vi börjar med att skanna en äldre version av Python-imagen. Vi skriver hela kommandot på en rad och trycker Enter:

```console
docker run --rm aquasec/trivy image python:3.9
```

`run --rm`
: startar en container av Trivy och tar bort den när den är klar.

`aquasec/trivy`
: är imagen som innehåller verktyget Trivy.

`image python:3.9`
: säger till Trivy att skanna imagen `python:3.9`. Trivy hämtar själv ner den image vi vill skanna, vi behöver alltså inte ladda ner den först.

Det tar en stund, eftersom Trivy laddar ner sin sårbarhetsdatabas. Vi väntar tills det är klart.

#### Steg 2 - Läsa resultatet

När Trivy är klar skriver den ut en tabell. Överst ser vi ett namn och sedan en sammanfattning som ser ut ungefär så här:

```console
python:3.9 (debian 12.x)
Total: 1234 (UNKNOWN: 0, LOW: 400, MEDIUM: 500, HIGH: 300, CRITICAL: 34)
```

Vi tittar särskilt på orden `HIGH` och `CRITICAL`. Det är de allvarliga sårbarheterna. Varje rad längre ner i tabellen är en enskild sårbarhet med ett id som börjar på `CVE-`, vilket står för *Common Vulnerabilities and Exposures*, ett globalt system för att namnge kända säkerhetshål.

Vi antecknar hur många `CRITICAL` och `HIGH` den här imagen har.

#### Steg 3 - Skanna en nyare och slimmad image

Nu skannar vi en nyare version av samma programvara, i en så kallad *slim*-variant som innehåller mindre programvara:

```console
docker run --rm aquasec/trivy image python:3.12-slim
```

När den är klar antecknar vi antalet `CRITICAL` och `HIGH` även här.

#### Steg 4 - Jämföra

Vi jämför de två resultaten. Vi ska nästan alltid se att den nyare, slimmade imagen har betydligt färre allvarliga sårbarheter. Anledningen är dubbel: den är nyare och har fått rättningar och den innehåller färre program, vilket betyder att det finns färre saker som kan vara sårbara.

> Det här är en viktig princip. Ju mindre vi packar in i en image, desto mindre *attackyta* har vi. En slimmad image är inte bara mindre i storlek, den är ofta också säkrare.

#### Steg 5 - Skanna en webbserver-image

För att se att det inte bara gäller Python skannar vi också en äldre webbserver:

```console
docker run --rm aquasec/trivy image nginx:1.20
```

Vi tittar på resultatet på samma sätt som tidigare.

### Kontrollfrågor

Vi svarar med egna ord:

1. Vad är skillnaden mellan en sårbarhet med nivån `LOW` och en med nivån `CRITICAL`?
2. Varför kan en image vi hämtat från ett officiellt håll ändå innehålla sårbarheter?
3. Vi hittade färre sårbarheter i den slimmade imagen. Ge två skäl till varför.
4. Koppling till kapitlet: vilket av *Cloud Security Alliance* hot handlar den här labben om?
5. Om vi bygger en tjänst på en image med 30 kritiska sårbarheter, vem är det som bär ansvaret för att åtgärda det, vi eller den som skapade imagen?

### Städa upp

Trivy startades med `--rm`, så själva Trivy-containern är redan borttagen. Trivy hämtade dock ner de images vi skannade. Vill vi ta bort dem för att spara plats skriver vi:

```console
docker rmi python:3.9 python:3.12-slim nginx:1.20
```

`rmi`
: står för "remove image" och tar bort en image från vår dator.

### Om du vill gå vidare

Vi kan prova att skanna en image vi själva använt i en tidigare kurs, eller en helt egen. Vi kan också be Trivy att bara visa de allvarligaste sårbarheterna genom att lägga till `--severity CRITICAL` sist i kommandot.

--- 

## Labb 2 - Var går ansvarsgränsen? (delat ansvar)

### Vad vi ska lära oss

I den här labben ska vi:

- förstå skillnaden mellan *IaaS*, *PaaS* och *SaaS*
- förstå vad *delat ansvar* betyder i praktiken
- själva rita en ansvarsmatris
- koppla ansvarsfördelningen till *Service Level Agreement*, SLA

Det här är den mest tankeinriktade av våra labbar. Vi kör ett par containrar för att göra det konkret, men det viktigaste arbetet sker i huvudet och på pappret.

## Bakgrund

När vi flyttar till molnet försvinner inte ansvaret, men det förskjuts. Leverantören tar hand om en del och vi tar hand om resten. Var gränsen går beror på vilken sorts tjänst vi köper.

- **IaaS** (*Infrastructure as a Service*) betyder att vi hyr grundläggande byggstenar som virtuella maskiner och lagring. Leverantören sköter hårdvaran, men vi sköter nästan allt ovanför, som operativsystem, uppdateringar och applikationer.
- **PaaS** (*Platform as a Service*) betyder att vi får en färdig plattform att köra vår kod på. Leverantören sköter även operativsystem och drift, medan vi ansvarar för vår egen kod och våra data.
- **SaaS** (*Software as a Service*) betyder att vi använder en färdig tjänst, till exempel ett mejlsystem. Leverantören sköter nästan allt, men vi ansvarar fortfarande för våra egna data och våra användarkonton.

En vanlig och farlig missuppfattning är att vi vid SaaS inte har något ansvar alls. Det stämmer inte. Vi ansvarar alltid för vem vi ger åtkomst till och för hur vi hanterar vår egen data.

### Steg för steg

#### Steg 1 - Kör en tjänst där vi har allt ansvar

Vi startar en databas i en container. Här är vi själva ansvariga för nästan allt, precis som vid *IaaS*.

```console
docker run -d --name egen-db -e POSTGRES_PASSWORD=Test1234 postgres:16
```

`-d`
: kör containern i bakgrunden, så att vi får tillbaka terminalen direkt.

`--name egen-db`
: ger containern ett namn så att vi lätt kan hänvisa till den.

`-e POSTGRES_PASSWORD=Test1234`
: sätter en miljövariabel inne i containern, i det här fallet lösenordet till databasen.

`postgres:16`
: är imagen med databasen PostgreSQL.

Nu funderar vi: vem ansvarar för att den här databasen uppdateras med säkerhetuppdateringar? Vem ansvarar för att lösenordet är starkt? Vem ansvarar för backup? Svaret är att allt det är vårt ansvar. Ingen annan gör det åt oss.

#### Steg 2 - Fundera på hur det hade sett ut som PaaS

I en riktig molntjänst kan vi i stället köpa en så kallad *managed* databas. Då hade leverantören skött uppdateringar, drift och backup, medan vi fortfarande ansvarat för vår data och vilka konton som får läsa den.

Vi behöver inte köra någon sådan tjänst här, men vi noterar skillnaden: samma databas, men en helt annan ansvarsfördelning beroende på hur vi köper den.

#### Steg 3 - Rita ansvarsmatrisen

Nu gör vi huvuduppgiften. Vi skapar en tabell med sju rader och tre kolumner. Raderna är sju saker som måste skötas, kolumnerna är de tre tjänstetyperna. I varje ruta skriver vi antingen *Leverantör*, *Vi* eller *Delat*.

Vi kopierar in den här mallen i ett eget dokument och fyller i den:

| Ansvarsområde | IaaS | PaaS | SaaS |
|:--|:--|:--|:--|
| Fysisk hårdvara | | | |
| Nätverk och brandvägg | | | |
| Operativsystem och uppdateringar | | | |
| Applikationskod | | | |
| Våra data | | | |
| Användarkonton och behörigheter | | | |
| Multifaktorautentisering (MFA) | | | |

Det finns inget enda rätt svar på varje ruta, men det finns mönster. Ju längre åt höger vi går, desto mer tar leverantören över. Men de nedersta raderna, våra data och våra konton, är i praktiken alltid vårt ansvar oavsett tjänstetyp. Det är den insikten vi vill komma fram till.

#### Steg 4 - Koppla till SLA

Ett *Service Level Agreement*, SLA, är det avtal som beskriver vad leverantören lovar oss, till exempel hur stor andel av tiden tjänsten ska vara tillgänglig. Vi funderar på följande och skriver ner våra svar:

- Om en leverantör lovar 99,9 procent tillgänglighet, hur mycket nedtid blir det per år? (Ledtråd: räkna ut hur många timmar 0,1 procent av ett år är.)
- Vad hjälper ett SLA oss med, och vad hjälper det oss inte med? Om vår verksamhet stod stilla i fyra timmar, får vi tillbaka den förlorade tiden?

### Kontrollfrågor

1. Förklara med egna ord skillnaden mellan IaaS, PaaS och SaaS.
2. Varför är det farligt att tro att vi inte har något säkerhetsansvar vid SaaS?
3. Vilken rad i ansvarsmatrisen är alltid vårt ansvar, oavsett tjänstetyp? Varför?
4. Vad är ett SLA, och vad garanterar det egentligen?

### Städa upp

Vi stoppar och tar bort databasen:

```console
docker stop egen-db
docker rm egen-db
```

### Om du vill gå vidare

Vi kan leta upp ett riktigt SLA från en stor molnleverantör på deras webbplats och läsa vad de faktiskt lovar. Ofta är siffrorna lägre och undantagen fler än vi tror.

## Labb 3 - En publik bucket (MinIO)

### Vad vi ska lära oss

I den här labben ska vi:

- starta en egen molnlagringstjänst lokalt med *MinIO*
- skapa en *bucket* och ladda upp en fil
- av misstag göra bucketen publik och se konsekvensen
- upptäcka exponeringen och sedan härda bucketen

### Bakgrund

Det här är kanske det vanligaste molnmisstaget av alla, och vi läste om det i scenariot *Publik lagring*. En *bucket* är en lagringsyta i molnet, ungefär som en mapp. Problemet uppstår när en bucket som ska vara privat i stället blir publik, så att vem som helst på internet kan läsa filerna om de känner till adressen.

Vi ska själva återskapa det misstaget, se hur lätt det sker, och sedan lära oss att stänga hålet. Verktyget vi använder heter *MinIO* och är en gratis lagringstjänst med öppen källkod som fungerar precis som molnlagringen hos de stora leverantörerna.

### Förberedelser

Vi behöver *Docker Desktop* igång och en öppen *PowerShell*.

### Steg för steg

#### Steg 1 - Starta MinIO

Vi startar MinIO med hela kommandot på en rad:

```console
docker run -d --name minio -p 9000:9000 -p 9001:9001 -e "MINIO_ROOT_USER=admin" -e "MINIO_ROOT_PASSWORD=Password123" minio/minio:RELEASE.2025-04-22T22-12-26Z server /data --console-address ":9001"
```

Vi går igenom de viktigaste delarna:

`-d`
: kör MinIO i bakgrunden.

`--name minio`
: döper containern till minio.

`-p 9000:9000`
: öppnar porten som själva lagringstjänsten svarar på.

`-p 9001:9001`
: öppnar porten till webbgränssnittet, det vi loggar in i.

`-e "MINIO_ROOT_USER=admin"` och `-e "MINIO_ROOT_PASSWORD=Password123"`
: sätter användarnamn och lösenord för administratören. Lösenordet måste vara minst åtta tecken, annars startar inte MinIO.

`minio/minio server /data --console-address ":9001"`
: talar om vilken image vi kör och att data ska lagras i mappen `/data` inne i containern.

> Jag valde att använda imagen `minio/minio:RELEASE.2025-04-22T22-12-26Z` här eftersom det är den sista där det går att ställa in access policy för en bucket i webui. Numera använder man kommandot `mc` för att ställa in detta, men jag ville att laborationen ska visa hur enkelt misstaget är att göra i molnplattformars gränssnitt.
>
> **ANVÄND INTE DENNA IMAGE OM DIN MINIO ÄR EXPONERAD DIREKT UT MOT INTERNET!**

#### Steg 2 - Logga in i webbgränssnittet

Vi öppnar en webbläsare och går till adressen:

```console
http://localhost:9001
```

Vi loggar in med användarnamnet `admin` och lösenordet `Password123`. Nu ser vi MinIO:s administrationsgränssnitt.

#### Steg 3 - Skapa en bucket

Vi klickar oss fram till *Buckets* i menyn till vänster och sedan på knappen *Create Bucket*. Vi döper bucketen till `kunddata` och klickar på *Create Bucket*.

> Vi använder namnet `kunddata` för att det ska kännas verkligt. Tänk att den här bucketen innehåller riktiga kunders avtal eller personuppgifter. Det är just den sortens data som aldrig ska ligga öppet.

#### Steg 4 - Ladda upp en fil

Vi skapar först en liten testfil på vår dator. I PowerShell skriver vi:

```console
"Hemlig kundinformation - personnummer och avtal" > hemligt.txt
```

Det här skapar en textfil som heter `hemligt.txt` i den mapp vi står i.

Nu går vi tillbaka till MinIO i webbläsaren, klickar på bucketen `kunddata`, klickar på *Upload* och väljer filen `hemligt.txt`. Filen laddas upp.

#### Steg 5 - Testa att den är privat

Innan vi gör något mer testar vi att filen är skyddad. Vi öppnar en ny flik i webbläsaren och går till:

```console
http://localhost:9000/kunddata/hemligt.txt
```

Vi ska nu få ett felmeddelande som säger något i stil med *Access Denied*. Bra. Det betyder att bucketen är privat och att ingen kan läsa filen utan att logga in.

#### Steg 6 - Gör misstaget

Nu gör vi felet som orsakar så många verkliga incidenter. Vi går tillbaka till MinIO, klickar på bucketen `kunddata` och letar upp inställningarna för bucketen (*Summary*). Vi ändrar *Access Policy* från *Private* till *Public* och sparar.

#### Steg 7 - Se konsekvensen

Vi går tillbaka till fliken med adressen:

```console
http://localhost:9000/kunddata/hemligt.txt
```

Vi laddar om sidan. Den här gången laddas filen ner eller visas direkt. Vi läser den känsliga texten utan att ha loggat in med något lösenord alls.

> Det här är hela poängen med labben. Ingen har hackat oss. Vi har inte blivit angripna. Vi har bara ändrat en enda inställning, och plötsligt ligger kunddatan öppen för vem som helst som känner till adressen. I ett riktigt moln hade den adressen legat på det öppna internet.

#### Steg 8 - Härda bucketen igen

Nu stänger vi hålet. Vi går tillbaka till MinIO och ändrar *Access Policy* på bucketen `kunddata` tillbaka till *Private* och sparar.

Vi laddar om adressen från steg 7 en sista gång. Nu ska vi återigen få *Access Denied*. Hålet är stängt.

### Kontrollfrågor

1. I labben blev filen exponerad utan att någon angripit oss. Vad var det egentligen som gick fel?
2. I det verkliga scenariot i kapitlet, varför är det så svårt att veta hur länge en bucket varit publik och vem som laddat ner data?
3. Vilken lag i Sverige och EU aktualiseras direkt om en publik bucket innehåller kunders personuppgifter?
4. Inom vilken tidsfrist måste en allvarlig personuppgiftsincident rapporteras, och till vilken myndighet?
5. Föreslå två konkreta sätt att förhindra att en bucket blir publik av misstag.

### Städa upp

```console
docker stop minio
docker rm minio
```

### Om du vill gå vidare

Vi kan skapa flera buckets och sätta olika policyer på dem, för att öva på att hålla ordning på vad som är öppet och vad som är stängt.

## Labb 4 - Läckta API-nycklar (gitleaks)

### Vad vi ska lära oss

I den här labben ska vi:

- förstå varför en *API-nyckel* aldrig ska ligga i källkoden
- skapa ett litet git-projekt och råka checka in en nyckel
- använda verktyget *gitleaks* för att hitta nyckeln
- upptäcka att nyckeln finns kvar i historiken även efter att vi tagit bort den

### Bakgrund

Vi läste om det här i scenariot *Läckta API-nycklar*. En *API-nyckel* är en hemlig sträng som fungerar som ett lösenord för en applikation. Ett vanligt misstag är att en utvecklare skriver in nyckeln direkt i koden för att det är enkelt, och sedan checkar in koden i ett git-repo. Automatiserade verktyg letar ständigt igenom publika repon efter just sådana nycklar och de kan hittas inom minuter.

Vi ska göra exakt det misstaget själva, i en trygg lokal miljö och sedan hitta nyckeln med samma sorts verktyg som angriparna använder. Verktyget heter *gitleaks* och är gratis med öppen källkod.

### Förberedelser

Vi behöver *Docker Desktop* igång och en öppen *PowerShell*. Vi behöver också *git* installerat. Vi kontrollerar det med:

```console
git --version
```

Får vi ett felmeddelande installerar vi git från [git-scm.com](https://git-scm.com/) och öppnar sedan ett nytt PowerShell-fönster.

### Steg för steg

#### Steg 1 - Skapa en projektmapp

Vi skapar en mapp för vårt lilla projekt och går in i den:

```console
mkdir apilabb
cd apilabb
```

`mkdir apilabb`
: skapar en ny mapp som heter apilabb.

`cd apilabb`
: går in i mappen. Nu står vi inne i den.

#### Steg 2 - Skapa ett git-repo

Vi gör mappen till ett git-projekt:

```console
git init
```

Om det är första gången vi använder git på datorn kan git be oss om namn och e-post. Vi skriver då:

```console
git config user.email "elev@skolan.se"
git config user.name "Elev"
```

#### Steg 3 - Skriv lite kod med en hemlig nyckel

Nu skapar vi en liten Python-fil som innehåller en hemlig nyckel, precis som en stressad utvecklare kan göra. Vi skapar filen med det här kommandot:

```console
echo 'aws_access_key_id = "AKIAQWERTYUIOPASDFGH"' > app.py
```

Det skapar filen `app.py` med en rad kod i. Strängen `AKIAQWERTYUIOPASDFGH` ser ut som en riktig AWS-nyckel, vilket är precis den sortens hemlighet som gitleaks letar efter. Den är ofarlig, det är ett exempelvärde, men den har rätt form.

> Vi använder aldrig en riktig, verklig nyckel i en övning. Vi använder ett exempelvärde som ser ut som en nyckel men inte går att använda mot någon verklig tjänst.

#### Steg 4 - Checka in koden

Vi lägger till filen och checkar in den, alltså sparar den i git-historiken:

```console
git add app.py
git commit -m "Lade till app"
```

`git add app.py`
: markerar filen för att checkas in.

`git commit -m "Lade till app"`
: sparar den i historiken med en beskrivande text.

Nu har vi gjort misstaget. Nyckeln ligger i vår git-historik.

#### Steg 5 - Kör gitleaks

Nu spelar vi angripare och letar efter nyckeln med gitleaks. Vi skriver hela kommandot på en rad:

```console
docker run --rm -v ${PWD}:/repo zricethezav/gitleaks:latest detect --source /repo -v
```

Vi går igenom kommandot:

`--rm`
: tar bort gitleaks-containern när den är klar.

`-v ${PWD}:/repo`
: delar in vår nuvarande mapp i containern under namnet `/repo`. Det är så gitleaks får tillgång till vårt projekt. `${PWD}` betyder "mappen jag står i just nu".

`zricethezav/gitleaks:latest`
: är imagen med verktyget.

`detect --source /repo -v`
: säger till gitleaks att leta igenom projektet i `/repo` och visa detaljer med `-v`.

> Notering för macOS och Linux: i stället för `${PWD}` skriver vi `$(pwd)`. Hela raden blir då `docker run --rm -v $(pwd):/repo zricethezav/gitleaks:latest detect --source /repo -v`. Kör vi Windows i det äldre CMD i stället för PowerShell skriver vi `%cd%` i stället för `${PWD}`.

#### Steg 6 - Läsa resultatet

Gitleaks skriver ut en varning för varje hemlighet den hittat. Vi ska se att den hittat vår nyckel i filen `app.py`, med information om vilken rad och vilken commit den fanns i. Den skriver också ut en sammanfattning av hur många läckor den hittade.

#### Steg 7 - Ta bort nyckeln och se att det inte räcker

Nu gör vi det många tror räcker. Vi tar bort nyckeln ur koden. Vi öppnar `app.py` i en textredigerare, till exempel *Anteckningar*, tar bort raden med nyckeln och sparar. Sedan checkar vi in ändringen:

```console
git add app.py
git commit -m "Tog bort nyckeln"
```

Nu kör vi gitleaks igen med exakt samma kommando som i steg 5. Vad ser vi?

Vi ser att gitleaks **fortfarande hittar nyckeln**. Anledningen är att git sparar hela historiken. Även om den senaste versionen av filen är ren, ligger den gamla versionen med nyckeln kvar i historiken och den historiken raderas inte för att vi ändrar filen i efterhand.

> Det här är den viktigaste lärdomen i labben. När en nyckel väl har checkats in är den läckt. Att ta bort den ur den senaste versionen hjälper inte. Den enda säkra åtgärden är att omedelbart återkalla nyckeln hos tjänsten och skapa en ny.

### Kontrollfrågor

1. Varför räcker det inte att ta bort en nyckel ur den senaste versionen av koden?
2. Vad är den korrekta första åtgärden om vi upptäcker att en riktig nyckel läckt?
3. Var ska hemligheter som API-nycklar lagras i stället för i koden? Nämn minst ett sätt.
4. Varför är även privata repon en risk för läckta nycklar?
5. I det verkliga scenariot i kapitlet, vilka konsekvenser kan en läckt nyckel få för ett företags kostnader?

### Städa upp

Gitleaks-containern togs bort automatiskt. Vill vi ta bort projektmappen går vi ur den och tar bort den:

```console
cd ..
Remove-Item -Recurse -Force apilabb
```

> Notering för macOS och Linux: där tar vi bort mappen med `rm -rf apilabb` i stället.

### Om du vill gå vidare

Många kodplattformar erbjuder automatisk skanning som stoppar en incheckning om den innehåller en nyckel. Vi kan läsa på om hur en sådan så kallad *pre-commit hook* fungerar och varför den fångar problemet innan det ens blir en läcka.

## Labb 5 - MFA som sista försvarslinje (Keycloak)

### Vad vi ska lära oss

I den här labben ska vi:

- starta en riktig identitetstjänst lokalt med *Keycloak*
- skapa en användare
- aktivera *multifaktorautentisering* (MFA)
- se hur MFA stoppar en inloggning även när lösenordet är känt

### Bakgrund

I kapitlet läste vi att svaga lösenord och saknad MFA hör ihop. Ett lösenord kan läcka på många sätt, genom phishing, genom ett intrång hos en annan tjänst eller genom ett oskyddat dokument. *MFA*, som står för *Multi-Factor Authentication*, är det som avgör om ett läckt lösenord faktiskt räcker för att ta sig in. Med MFA behöver angriparen inte bara lösenordet, utan även en engångskod från vår telefon.

Vi ska sätta upp en riktig identitetstjänst, *Keycloak*, som är gratis med öppen källkod, och själva känna på skillnaden mellan att logga in med bara lösenord och att logga in med MFA.

### Förberedelser

Vi behöver *Docker Desktop* igång och en öppen *PowerShell*. Vi behöver också en telefon med en autentiseringsapp, till exempel *Google Authenticator*, *Microsoft Authenticator* eller *FreeOTP*. Har vi ingen telefon nära går det också bra med en autentiseringsapp för datorn.

### Steg för steg

#### Steg 1 - Starta Keycloak

Vi startar Keycloak med hela kommandot på en rad:

```console
docker run -d --name keycloak -p 8080:8080 -e KEYCLOAK_ADMIN=admin -e KEYCLOAK_ADMIN_PASSWORD=admin quay.io/keycloak/keycloak:latest start-dev
```

`-p 8080:8080`
: öppnar porten till Keycloaks webbgränssnitt.

`-e KEYCLOAK_ADMIN=admin` och `-e KEYCLOAK_ADMIN_PASSWORD=admin`
: sätter administratörens användarnamn och lösenord.

`start-dev`
: startar Keycloak i utvecklingsläge, vilket är enklare och räcker för vår labb.

Keycloak tar en liten stund att starta första gången. Vi väntar ungefär en minut.

#### Steg 2 - Logga in som administratör

Vi öppnar en webbläsare och går till:

```console
http://localhost:8080
```

Vi klickar på *Administration Console* och loggar in med `admin` och `admin`.

#### Steg 3 - Skapa ett realm

Ett *realm* i Keycloak är en avgränsad värld med sina egna användare och inställningar. Vi skapar ett eget så att vi inte rör administratörsdelen.

Uppe till vänster står det troligen *Manage realms*. Vi klickar på den och väljer sedan *Create realm*. Vi döper det till `labb` och klickar på *Create*.

#### Steg 4 - Skapa en användare

Vi ser till att vi står i realmet `labb` (det syns uppe till vänster). Vi klickar på *Users* i menyn och sedan på *Create new user*. Vi fyller i:

- *Username*: `anna`
- Vi klickar på *Create*.

Nu behöver användaren ett lösenord. Vi klickar på fliken *Credentials* och sedan på *Set password*. Vi skriver ett lösenord, till exempel `Sommar2026`, i båda fälten. Vi stänger av valet *Temporary* så att lösenordet gäller direkt. Vi klickar på *Save* och bekräftar.

#### Steg 5 - Logga in utan MFA

Vi ska nu testa att logga in som Anna, men vi vill inte blanda ihop det med vår administratörsinloggning. Enklast är att öppna ett *privat fönster* eller *inkognitofönster* i webbläsaren. Där går vi till kontosidan för vårt realm:

```console
http://localhost:8080/realms/labb/account
```

Vi loggar in med `anna` och lösenordet `Sommar2026`. Vi behöver sätta ett nytt lösenord och sedan ange e-post och namn (hitta på något bara). Det viktiga att ta med sig härifrån är att vi kunde logga in med enbart användarnamn och lösenord.

> Tänk nu att det här lösenordet läckt, till exempel via ett phishing-mejl. Just nu hade en angripare kommit rakt in, precis som vi gjorde. Det är det vi ska ändra på.

Vi loggar ut igen. Klicka på namnet högst upp till höger och välj Sign out.

#### Steg 6 - Kräv MFA

Nu går vi tillbaka till administratörsfliken. I realmet `labb` klickar vi på *Authentication* i menyn och sedan på fliken *Required actions*. Vi letar upp raden *Configure OTP* och ser till att den är aktiverad och satt som *Default action* så att nya inloggningar tvingas sätta upp MFA.

Sedan går vi in på *Users* och väljer *anna* igen. I fältet *Required user actions* lägger vi till *Configure OTP*. Klicka sedan *Save*.

> *OTP* står för *One-Time Password*, alltså den engångskod som autentiseringsappen visar och som byts ut med jämna mellanrum.

#### Steg 7 - Logga in med MFA

Vi går tillbaka till vårt privata fönster och till kontosidan igen:

```console
http://localhost:8080/realms/labb/account
```

Vi loggar in med `anna` och det lösenord vi satte. Den här gången händer något nytt. Keycloak visar en QR-kod och ber oss koppla en autentiseringsapp. Vi öppnar autentiseringsappen på telefonen, väljer att lägga till ett konto och skannar QR-koden. Appen börjar visa en sexsiffrig kod som byts ut var trettionde sekund. Vi skriver in den koden i Keycloak och slutför.

#### Steg 8 - Se att lösenordet inte längre räcker

Vi loggar ut och loggar in igen med `anna` och vårt lösenord. Nu räcker inte lösenordet. Keycloak ber oss också om engångskoden från appen. Utan telefonen kommer vi inte in, även om vi kan lösenordet.

> Det här är hela poängen. Ett läckt lösenord är inte längre nog. Angriparen skulle också behöva vår telefon. MFA förvandlar ett komprometterat lösenord från en katastrof till ett stoppat inloggningsförsök.

### Kontrollfrågor

1. Med egna ord, vad är skillnaden mellan att logga in med bara lösenord och med MFA?
2. Varför skyddar MFA även om vårt lösenord har läckt?
3. Nämn tre sätt ett lösenord kan läcka på, enligt kapitlet.
4. Är MFA ett hundraprocentigt skydd? Fundera på om det finns sätt att kringgå det ändå.
5. Varför är det extra viktigt att administratörskonton har MFA?

### Städa upp

Vi stoppar och tar bort Keycloak:

```console
docker stop keycloak
docker rm keycloak
```

### Om du vill gå vidare

Vi kan i administratörsgränssnittet titta på inställningarna för lösenordspolicy under *Authentication*, *Policies* och *Password policy* och fundera på vilka krav som är rimliga att ställa på ett lösenord.

## Labb 6 - Riskbedömning och policy (grupparbete)

### Vad vi ska lära oss

I den här labben ska vi, i grupp:

- gå igenom en färdig men medvetet osäker molnmiljö
- systematiskt identifiera säkerhetsriskerna
- göra en riskbedömning i ett riskregister
- skriva en enkel säkerhetspolicy och en åtgärdsplan

Här knyter vi ihop det vi lärt oss i de tidigare labbarna. Vi skriver inte så mycket kod, i stället tränar vi på att tänka som en säkerhetsspecialist.

### Bakgrund

En stor del av rollen som IT-säkerhetsspecialist handlar inte om att trycka på knappar, utan om att bedöma risker och skriva ner hur organisationen ska förhålla sig till dem. En *riskbedömning* går ut på att lista vad som kan gå fel, hur troligt det är och hur allvarligt det vore. En *policy* är ett dokument som beskriver vad som gäller, till exempel att alla konton ska ha MFA.

Vi ska få en färdig miljö som är full av precis de misstag vi läst om i kapitlet, och vår uppgift är att hitta dem, bedöma dem och föreslå hur de ska åtgärdas.

### Förberedelser

Vi behöver *Docker Desktop* igång. Vi arbetar i grupp, gärna tre till fyra personer. En i gruppen sköter datorn, resten läser och diskuterar. Vi väljer också en som skriver ner det vi kommer fram till.

### Del 1 - Studera den osäkra miljön

#### Steg 1 - Skapa en projektmapp

```console
mkdir riskbedomning
cd riskbedomning
```

#### Steg 2 - Skapa miljöfilen

Vi skapar en fil som beskriver en hel liten molnmiljö med flera tjänster. Den heter `docker-compose.yml`. Vi skapar den genom att klistra in innehållet nedan i en textredigerare och spara filen som `docker-compose.yml` i mappen `riskbedomning`.

> Vi tittar noga på filen medan vi skapar den. Den är full av dåliga val. En del av uppgiften är att hitta dem.

```yaml
services:
  lagring:
    image: minio/minio:RELEASE.2025-04-22T22-12-26Z
    ports:
      - "9000:9000"
      - "9001:9001"
    environment:
      MINIO_ROOT_USER: admin
      MINIO_ROOT_PASSWORD: admintest
    command: server /data --console-address ":9001"

  databas:
    image: postgres:12
    ports:
      - "5432:5432"
    environment:
      POSTGRES_PASSWORD: postgres

  webb:
    image: nginx:1.18
    ports:
      - "80:80"
```

#### Steg 3 - Starta miljön

Vi startar hela miljön med ett kommando:

```console
docker compose up -d
```

`compose up`
: startar alla tjänster som är beskrivna i filen `docker-compose.yml` samtidigt.

`-d`
: kör dem i bakgrunden.

Vi kontrollerar att allt kör:

```console
docker compose ps
```

Vi kan nu logga in i lagringen på `http://localhost:9001` med `admin` och `admintest` för att bekräfta att den svaga inloggningen faktiskt fungerar.

#### Del 2 - Identifiera riskerna

Nu är det dags för det viktiga arbetet. Gruppen går igenom miljön och letar efter allt som är osäkert. Vi använder kapitlet och de tidigare labbarna som stöd. Här är några frågor att utgå från, men vi letar också efter sådant som inte står i listan:

- Hur ser lösenorden ut i `docker-compose.yml`? Är de starka? Var ligger de?
- Är versionerna av programvaran (`postgres:12`, `nginx:1.18`) nya eller gamla? Vad betyder det för sårbarheter?
- Vilka portar är öppna och behöver de vara det?
- Finns det någon MFA?
- Finns det någon loggning?
- Vad vet vi om behörigheter och least privilege här?
- Vad händer med data om en container tas bort? Finns det backup?

Vi kan också köra en Trivy-skanning mot en av imagerna, till exempel:

```console
docker run --rm aquasec/trivy image postgres:12
```

### Del 3 - Skriv riskregistret

Vi sammanställer det vi hittat i ett *riskregister*. Vi skapar en tabell, en rad per risk. Vi bedömer *sannolikhet* och *konsekvens* på en enkel skala: Låg, Medel eller Hög. Vi använder den här mallen och fyller på med egna rader:

| Risk | Var i miljön | Sannolikhet | Konsekvens | Föreslagen åtgärd |
|:--|:--|:--|:--|:--|
| Svagt lösenord på lagringen | lagring | Hög | Hög | Byt till starkt lösenord, inför MFA |
| Hårdkodade lösenord i filen | docker-compose.yml | Hög | Hög | Flytta till hemlighetshanterare |
| Gamla programversioner | databas, webb | Medel | Hög | Uppdatera till nyare versioner |
| | | | | |
| | | | | |

Vi ska sikta på minst åtta rader. Det finns fler risker än de tre vi fyllt i som exempel.

### Del 4 - Skriv en enkel policy

Utifrån riskerna skriver vi en kort säkerhetspolicy för hur en molnmiljö ska sättas upp i vår påhittade organisation. Policyn ska vara konkret och gå att följa. Exempel på punkter en policy kan innehålla:

- Alla konton ska ha ett lösenord på minst tolv tecken och MFA aktiverat.
- Inga lösenord eller nycklar får förekomma i klartext i konfigurationsfiler.
- Endast nödvändiga portar får exponeras.
- All programvara ska hållas uppdaterad och skannas regelbundet.
- Varje konto ska tilldelas minsta möjliga behörighet.

Vi skriver vår egen version, anpassad efter de risker vi hittade. Vi motiverar varje punkt kort.

### Redovisning

Grupparbetet redovisas skriftligt. Redovisningen ska innehålla:

1. Riskregistret med minst åtta identifierade risker, bedömda och med åtgärder.
2. Säkerhetspolicyn med motiveringar.
3. En kort text där gruppen självständigt bedömer vilken data som skulle kunna hanteras i en sådan här miljö  och vilken data som absolut inte borde det förrän riskerna åtgärdats.

> Det sista är det viktigaste. Att kunna säga "den här datan kan vi hantera här, men den här får vi inte lägga in förrän vi åtgärdat de här sakerna" är precis den bedömning en IT-säkerhetsspecialist behöver kunna göra.

### Städa upp

Vi river hela miljön med ett kommando:

```console
docker compose down
```

`compose down`
: stoppar och tar bort alla containrar som `docker compose up` startade.

Sedan går vi ur mappen med `cd ..`.

### Om du vill gå vidare

Gruppen kan byta riskregister med en annan grupp och granska varandras arbete. Att få syn på en risk någon annan missat, eller att få sitt eget arbete granskat, är en stor del av hur säkerhetsarbete fungerar i verkligheten.
