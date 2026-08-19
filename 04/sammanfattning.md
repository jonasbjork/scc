> Detta dokument är AI-genererat och kan innehålla fel

# Sammanfattning: Hemligheter, nycklar och kryptering

Det här är en sammanfattning av kapitlet om hemligheter, nycklar och kryptering. Tanken är att vi ska kunna använda den som en snabb repetition inför tentan och som ett uppslagsverk när vi behöver påminna oss om skillnaden mellan till exempel symmetrisk och asymmetrisk kryptering, eller mellan en KMS och en HSM.

I den här sammanfattningen går vi igenom:

- vad en *secret* är och hur den skiljer sig från vanlig konfiguration
- symmetrisk och asymmetrisk kryptering
- hashning och signering
- TLS, HTTPS och certifikat
- de tre datatillstånden: *at rest*, *in transit* och *in use*
- nyckelhantering med KMS, HSM, rotation och BYOK
- vad kryptering faktiskt skyddar mot, och vad den inte gör

---

## Vad är en secret?

En *secret*, eller hemlighet, är information som ger åtkomst, bevisar identitet eller skyddar data. Det som gör en secret speciell är att den räcker i sig själv. Hamnar den hos fel person kan den användas direkt, utan att någon behöver bryta sig in eller använda avancerad teknik.

Exempel på secrets är lösenord, API-nycklar, access tokens, privata SSH-nycklar, databaslösenord, servicekonton, krypteringsnycklar och certifikat med tillhörande privat nyckel.

### Secret eller vanlig konfiguration?

Det här är en skillnad som är lätt att missa. Vanlig konfiguration är information som applikationen behöver för att fungera, men som inte i sig ger någon åtkomst. En secret är information som räcker för att agera som en betrodd identitet eller komma åt skyddad data.

| Vanlig konfiguration | Secret |
|---|---|
| `db.hostname = db.example.com` | `db.password = hemligt123` |
| `server.port = 5432` | `api.token = eyJhbGci...` |
| `environment = prod` | `signing.key = -----BEGIN RSA...` |

Den vänstra kolumnen kan vi checka in i Git och dela med kollegor. Den högra kolumnen ska aldrig hamna där.

> Temporärt blir alltid permanent. En secret som lades i ett gitrepo för att lösa ett akut problem en fredagskväll tenderar att ligga kvar i versionshistoriken i månader, även efter att filen tagits bort. Det räcker inte att ta bort filen - hemligheten finns kvar i historiken och måste betraktas som komprometterad.

---

## Symmetrisk kryptering

Vid *symmetrisk kryptering* används samma nyckel för att både kryptera och dekryptera. Vi kan tänka på det som ett kassaskåp med ett kombinationslås: den som känner till kombinationen kan både låsa och låsa upp.

Symmetrisk kryptering är snabb och effektiv, vilket gör den perfekt för att skydda data som lagras, alltså *data at rest*. Vi använder den för att kryptera filer, fält i en databas, säkerhetskopior eller hela hårddiskar. Den används också inuti TLS för att kryptera själva dataflödet.

Metoden har två svagheter vi behöver känna till:

- **Nyckeldelning** - eftersom samma nyckel används i båda ändar måste den på något sätt överföras säkert mellan parterna. Det är precis det problem som asymmetrisk kryptering löser.
- **En läckt nyckel är allvarlig** - till skillnad från ett lösenord som kan bytas ut kan en läckt krypteringsnyckel användas för att dekryptera all data som krypterats med den, inklusive historisk data och säkerhetskopior.

> En krypteringsnyckel som förvaras i samma fil som den krypterade datan är som att skriva kassaskåpskombinationen på en lapp som sitter på kassaskåpsdörren. Nycklar ska roteras regelbundet och förvaras skilt från datan de skyddar.

---

## Asymmetrisk kryptering

Vid *asymmetrisk kryptering* använder vi ett *nyckelpar* i stället för en enda delad nyckel. De två nycklarna är matematiskt kopplade till varandra:

- den *publika nyckeln* kan delas fritt med hela världen
- den *privata nyckeln* ska aldrig lämna sin ägare

Sambandet fungerar så här: det som krypteras med den publika nyckeln kan bara dekrypteras med den privata nyckeln, och det som signeras med den privata nyckeln kan verifieras med den publika. Vi kan tänka på det som ett öppet hänglås som vi delar ut till alla. Vem som helst kan låsa med det, men bara vi har nyckeln som låser upp.

Asymmetrisk kryptering ligger bakom TLS/HTTPS, SSH-autentisering, certifikat, digital signering och nyckelutbyte.

Metoden är kraftfull men beräkningstung och långsam. Därför kombinerar vi de två metoderna i praktiken: vi använder asymmetrisk kryptering för att säkert komma överens om en symmetrisk nyckel, och sedan tar den snabba symmetriska krypteringen vid för själva datatrafiken. Det är precis så TLS fungerar.

> Den publika nyckeln behöver inte skyddas alls - den är till för att delas. Den privata nyckeln är en secret i ordets fulla bemärkelse. Om den läcker måste vi omedelbart återkalla allt som är kopplat till den och skapa ett nytt nyckelpar. En privat nyckel ska aldrig skickas via e-post eller chatt, och aldrig checkas in i ett gitrepo - inte ens ett privat.

---

## Hashning

Hashning är något fundamentalt annorlunda än kryptering. Kryptering är en tvåvägsfunktion: vi krypterar och dekrypterar. En *hashfunktion* är en *envägsfunktion*. Den tar indata av godtycklig storlek och producerar ett värde av fast längd, ett *hashvärde* eller en *digest*, och det finns ingen väg tillbaka till ursprungsdatan.

Vi kan tänka på det som en köttkvarn. Vi kan stoppa in vad som helst och få ut något väldefinierat, men vi kan inte stoppa in resultatet och få tillbaka originalet.

Hashning används bland annat till:

- **Filintegritet** - vi laddar ner en fil, beräknar hashvärdet lokalt och jämför med det publicerade värdet. Stämmer de överens vet vi att filen inte ändrats. En enda ändrad bit ger ett helt annat hashvärde.
- **Lösenordslagring** - vi lagrar aldrig lösenord i klartext och inte heller krypterade. Vi lagrar hashvärdet. Vid inloggning hashar vi det angivna lösenordet och jämför.
- **Digitala signaturer** - bygger på hashning i kombination med asymmetrisk kryptering.

För säker lösenordslagring behöver vi två extra ingredienser:

- *salt* - ett slumpmässigt värde som läggs till lösenordet före hashningen, så att två lika lösenord får olika hashvärden. Det omöjliggör attacker med *rainbow tables*.
- en *långsam* hashalgoritm designad för lösenord, som *bcrypt*, *scrypt* eller *Argon2*, i stället för en snabb algoritm som SHA-256.

> SHA-256 är i dag standard för integritetskontroll och signaturer. Äldre SHA-1 och ännu äldre MD5 ska inte användas för säkerhetsändamål - de har kända svagheter. Vi stöter fortfarande på MD5-checksummor i gamla system, men vi ska inte lita på dem för säkerhetskritiska kontroller.

En vanlig missuppfattning är att ett hashat lösenord är oknäckbart. Det är svårknäckt, inte omöjligt. Ett svagt lösenord som `123456` är sårbart oavsett hashalgoritm, och hashning döljer inte innehåll - den bevisar bara att något inte ändrats. För att dölja innehåll behöver vi kryptering.

---

## Signering

Signering kombinerar kryptering och hashning och löser ett problem som ingen av dem klarar ensam: att bevisa vem som skapade något och att innehållet inte ändrats sedan dess. En digital signatur ger oss två garantier samtidigt:

- *Autenticitet* - det var verkligen den påstådda avsändaren som skapade eller godkände innehållet.
- *Integritet* - innehållet är exakt detsamma som när det signerades.

Mekanismen är enkel när vi väl kan hashning och asymmetrisk kryptering: avsändaren hashar innehållet och krypterar hashvärdet med sin privata nyckel. Det krypterade hashvärdet är signaturen. Mottagaren dekrypterar signaturen med avsändarens publika nyckel, hashar innehållet på sin sida och jämför. Stämmer hashvärdena överens är både identitet och integritet verifierade.

Det eleganta är att vi bara krypterar hashvärdet, inte hela innehållet. Det gör signering snabbt även för mycket stora filer.

Vi möter signering i:

- *signerade container-images* - så att ett Kubernetes-kluster kan verifiera att imagen byggdes av rätt pipeline och inte manipulerats i registret
- *signerade Git-commits* - en signatur bevisar att committen kom från någon med en specifik privat nyckel, till skillnad från fältet `author` som vem som helst kan sätta
- *signerade certifikat* - grunden för att vi kan lita på webbplatser och tjänster
- *signerade programvarupaket* - därför kan vi lita på att ett paket från ett paketarkiv kommer från rätt källa

> Signering skyddar inte innehållet från att läsas - det är krypteringens uppgift. Signering bevisar ursprung och integritet. Vi kan signera ett publikt dokument som alla får läsa, och kryptera ett privat dokument som ingen utom mottagaren ska kunna läsa. Olika verktyg för olika problem, men de kan kombineras.

---

## TLS, HTTPS och certifikat

*Transport Layer Security*, *TLS*, är protokollet som skyddar kommunikationen mellan en klient och en server. När vi ser `https://` i adressfältet är det TLS som arbetar under ytan.

TLS ger oss tre saker:

- *Kryptering* - trafiken kan inte läsas av någon som avlyssnar förbindelsen
- *Serverautentisering* - klienten kan verifiera att den pratar med rätt server, tack vare certifikatet
- *Integritet* - varje meddelande är skyddat mot manipulation på vägen

### Vad webbläsaren kontrollerar

När vi surfar till en `https://`-adress gör webbläsaren en serie kontroller innan den visar något innehåll:

- *Pratar jag med rätt server?* Certifikatet innehåller serverns identitet och publika nyckel.
- *Är certifikatet utfärdat av någon jag litar på?* Det ska vara signerat av en *Certificate Authority* (*CA*) som finns i webbläsarens inbyggda lista.
- *Är certifikatet giltigt just nu?* Ett utgånget certifikat accepteras inte.
- *Matchar certifikatet domännamnet?* Ett certifikat för `karoshi.se` gäller inte för `malware.com`.

Klarar certifikatet alla kontroller etableras anslutningen. Misslyckas någon kontroll varnar webbläsaren, och det är en varning vi ska ta på allvar.

Det som händer i bakgrunden kallas *TLS-handskakning*: klient och server kommer överens om algoritmer, servern presenterar sitt certifikat, klienten verifierar det, och parterna använder asymmetrisk kryptering för att komma överens om en gemensam symmetrisk nyckel för sessionen. Sedan tar den symmetriska krypteringen vid för all datatrafik.

> HTTP utan S skickar all trafik i klartext - lösenord, sessionskakor och personuppgifter går att läsa för den som lyssnar. TLS ska vara standard överallt, inte ett undantag för känsliga sidor. Det gäller även intern trafik mellan tjänster i ett privat nätverk.

---

## Certifikat och certifikatkedjan

Ett certifikat binder ihop en identitet med en publik nyckel på ett sätt som en betrodd tredje part har gått i god för. Det löser problemet: hur vet vi att den publika nyckeln vi tar emot verkligen tillhör den vi tror?

Ett certifikat innehåller:

- *namnet* - vad certifikatet gäller för, oftast ett domännamn (kan vara ett wildcard som `*.karoshi.se`)
- *den publika nyckeln* - hjärtat i certifikatet
- *utfärdaren* - vilken CA som signerat
- *giltighetstiden* - moderna certifikat har ofta korta giltighetstider, ett år eller kortare
- *organisationsinformation* - i certifikat med högre valideringsnivå
- *signaturen från CA:n* - det som gör allt ovanstående trovärdigt

### Certifikatkedjan

Webbläsaren kan omöjligt känna till varje certifikat för varje webbplats. Lösningen är hierarkisk tillit i tre nivåer:

- *Rot-certifikatet* - ett litet antal organisationer (till exempel DigiCert, Let's Encrypt, Comodo) vars certifikat är inbyggda i operativsystem och webbläsare. Rot-CA:er skyddar sina privata nycklar extremt hårt, ofta i hårdvarusäkrade moduler, och signerar sällan certifikat direkt.
- *Intermediate-certifikatet* (mellanliggande) - bryggan mellan roten och webbplatsen. Om en mellanliggande CA komprometteras kan den återkallas utan att rot-certifikatet berörs.
- *Servercertifikatet* - det certifikat som webbplatsen presenterar, signerat av en mellanliggande CA och giltigt för en specifik domän.

När webbläsaren tar emot ett servercertifikat följer den kedjan hela vägen upp till en rot-CA den känner igen. Finns rot-CA:n i listan är kedjan komplett och anslutningen godkänns.

> En server som bara skickar sitt eget certifikat utan de mellanliggande CA-certifikaten kan orsaka verifieringsfel, eftersom webbläsaren inte kan bygga kedjan hela vägen upp till roten. Det är ett vanligt konfigurationsmisstag. Serverkonfigurationen måste inkludera hela certifikatkedjan.

Ett certifikat kan behöva *återkallas* innan det går ut, till exempel om den privata nyckeln komprometteras. För det finns *CRL* (*Certificate Revocation List*) och *OCSP* (*Online Certificate Status Protocol*). Återkallelse har dock kända utmaningar i praktiken, vilket är ett av skälen till att korta giltighetstider föredras - ett certifikat som snart går ut behöver sällan återkallas.

---

## Data at rest, in transit och in use

Data befinner sig i ett av tre tillstånd, och varje tillstånd kräver sitt eget skydd.

### Data at rest

*Data at rest* är data som ligger lagrad och väntar: databaser, säkerhetskopior, loggar, diskar i virtuella maskiner. Om en angripare når lagringssystemet är okrypterad data direkt läsbar. Kryptering är det som gör den oanvändbar.

Vi skyddar data at rest med flera nivåer, ofta samtidigt:

- *Diskkryptering* - skyddar hela disken mot fysiska hot, som en stulen eller felaktigt kasserad disk
- *Databaskryptering* - antingen hela lagringsfilen eller enskilda fält och kolumner (till exempel just personnummer)
- *Objektlagringskryptering* - standard hos de flesta molnleverantörer i dag
- *Krypterade säkerhetskopior* - ett område där det ofta brister, backupen behöver samma skydd som produktionsmiljön

> Loggar förtjänar extra uppmärksamhet. De samlar känslig information som IP-adresser och sessionsidentifierare, och ibland råkar ett lösenord eller en token hamna i ett loggmeddelande. Okrypterade loggar är ett förbisett säkerhetsproblem.

### Data in transit

*Data in transit* är data på väg via ett nätverk vi ofta inte kontrollerar. Det grundläggande hotet är avlyssning, en passiv attack som inte lämnar spår. Vi skyddar trafiken med:

- *TLS och HTTPS* - standardskyddet för webb- och API-trafik
- *mTLS* (*mutual TLS*) - båda parter verifierar varandras identitet, starkt skydd mellan tjänster i ett kluster
- *SSH* - standard för administrativ åtkomst till linux-servrar, helst med nyckelpar i stället för lösenord
- *VPN* och det modernare *WireGuard* - krypterad tunnel mellan klient och nätverk
- *IPsec* - kryptering på nätverksnivå, ofta mellan datacenter
- *krypterade databaskopplingar* - PostgreSQL och MySQL stöder TLS, men det är inte alltid på som standard

> En vanlig missuppfattning är att intern trafik inte behöver krypteras "eftersom den sker inom nätverket". Det är precis det Zero Trust ifrågasätter. Är en angripare väl inne är okrypterad intern trafik lika läsbar som okrypterad internettrafik. Intern trafik kräver samma skydd som extern.

### Data in use

*Data in use* är data som aktivt behandlas i minne eller av processorn. Det här är det svåraste tillståndet, för data in use är per definition dekrypterad. Vi kan inte kryptera oss ur problemet på samma sätt som för de andra två tillstånden, utan måste tänka annorlunda.

Eftersom vi inte kan kryptera data som används handlar skyddet om att begränsa exponering och isolera behandlingen:

- *Minsta möjliga åtkomst* - en process ska bara ha tillgång till den data den faktiskt behöver, bara så länge uppdraget pågår
- *Isolering* - containerisering, virtualisering och nätverkssegmentering begränsar vad en komprometterad process kan nå
- *Konfidentiell databehandling* (*confidential computing*) - *Trusted Execution Environments* (*TEE*) i hårdvaran, där kod körs skyddad även från operativsystemet och hypervisorn
- *Begränsad loggning* - undvik att skriva känsliga värden till loggar
- *Temporära filer* - mellanresultat på disk kan hamna i okrypterade tempfiler som lever längre än processen

> Kryptering skyddar inte data som aktivt används - det är ett fundamentalt villkor oavsett hur bra krypteringen är i övrigt. Samtidigt är data in use sällan det lättaste angreppsalternativet. En angripare som kan dumpa minne på en produktionsserver har redan tagit sig förbi många andra lager. Det är ett av flera skyddslager, inte ett ensamt problem att lösa isolerat.

---

## Nyckelhantering

Kryptering löser ett problem och skapar ett annat: var förvarar vi nycklarna? En nyckel som ligger bredvid den krypterade datan är inte till mycket hjälp.

### KMS - Key Management Service

En *Key Management Service* (*KMS*) är en dedikerad tjänst vars enda uppgift är att hantera kryptografiska nycklar säkert. En KMS kan:

- *skapa nycklar* med kryptografiskt säker slumpmässighet
- *lagra nycklar säkert* så att de aldrig exponeras i klartext utanför tjänsten
- *kontrollera vem som får använda nycklar* med finkornig åtkomstkontroll (en app får kryptera men inte dekryptera, en roll får rotera men inte använda, och så vidare)
- *logga nyckelanvändning* för full spårbarhet
- *rotera nycklar* automatiskt enligt schema
- *separera data från nycklar* - kanske den viktigaste designprincipen

I molnet finns AWS KMS, Azure Key Vault och Google Cloud KMS. Ett plattformsoberoende alternativ är *HashiCorp Vault* och dess öppna variant *OpenBao*, som hanterar inte bara krypteringsnycklar utan hemligheter av alla slag. OpenBao är populärt i miljöer där vi inte vill låsa oss till en enskild leverantör.

### HSM - Hardware Security Module

En *Hardware Security Module* (*HSM*) är dedikerad hårdvara byggd specifikt för att lagra och använda kryptografiska nycklar. Det centrala är att nyckeln aldrig lämnar hårdvaran i klartext. Vi skickar in data, krypteringen sker inuti den skyddade hårdvaran, och vi får tillbaka resultatet. Försöker någon fysiskt bryta sig in nollställer enheten sig själv.

Det är lätt att blanda ihop begreppen:

- en *KMS* är **tjänsten** - systemet vi pratar med för att skapa, lagra, rotera och styra åtkomst
- en *HSM* är **hårdvaran** - miljön där nycklarna faktiskt skyddas längst ner i stacken

En KMS kan använda en HSM som underliggande lagring, men kan också lagra nycklarna med mjukvara.

> Om en nyckel är så viktig att ett intrång skulle innebära att vi måste kontakta en tillsynsmyndighet, en banks säkerhetsavdelning eller ett lands regering, hör nyckeln hemma i ett HSM. Banker, certifikatutfärdare, myndigheter och kritisk infrastruktur använder HSM som standardkrav.

### Nyckelrotation

*Nyckelrotation* är principen att regelbundet byta ut nycklar, inte för att vi vet att de läckt, utan för att begränsa skadan om de någon gång gör det. Tänk på det som att byta lås på kontoret med jämna mellanrum.

Vi roterar nycklar för att:

- *begränsa skadan vid läckage* - en nyckel som roteras var trettionde dag ger en angripare ett maximalt tidsfönster på trettio dagar
- *uppfylla regelverk* - PCI DSS, HIPAA med flera ställer ofta explicita krav på rotationsintervall
- *stänga ute gamla system* - ett sätt att effektivt låsa ut ett gammalt system utan att röra åtkomstkontrollen
- *minska livslängden på credentials* - samma princip som tidsbegränsad åtkomst i IAM

Rotation kostar dock. Har en applikation en nyckel hårdkodad kräver varje rotation en ny driftsättning. En väldesignad KMS håller reda på flera nyckelversioner samtidigt: ny data krypteras med senaste versionen medan gammal data fortfarande kan dekrypteras med rätt äldre version. Applikationen behöver inte ens känna till rotationen.

> En roteringsplan som aldrig testas är ingen plan. Vi behöver veta att systemen faktiskt klarar rotation utan driftstopp när det väl sker. Det bör ingå i våra disaster recovery-övningar, inte bara i dokumentationen.

### BYOK - Bring Your Own Key

*Bring Your Own Key* (*BYOK*) innebär att vi själva äger och kontrollerar krypteringsnycklarna, även när datan lagras hos en molnleverantör. Vi skapar nyckeln i vår egen miljö och laddar upp den till molntjänsten.

BYOK ger oss mer kontroll (vi kan återkalla åtkomsten och effektivt neka leverantören tillgång utan att radera data), hjälper oss uppfylla compliancekrav och ger en tydligare ansvarsfördelning.

> BYOK flyttar kontroll till oss, men också ansvar. Förlorar vi våra egna nycklar är all data krypterad med dem permanent oåtkomlig - det finns ingen support att ringa. BYOK kräver testade processer för backup, rotation och återställning. En organisation som inte redan har etablerade rutiner för nyckelhantering bör bygga upp dem innan BYOK aktiveras, inte efteråt.

---

## Vad kryptering skyddar mot - och inte skyddar mot

Det är lätt att tro att krypterad data automatiskt är säker. Det stämmer inte, och det är viktigt att veta var gränsen går.

### Kryptering skyddar mot

- *avlyssning av trafik* - en angripare på nätverket ser bara krypterat brus
- *läsning av stulna diskar* - en disk ur en server eller en bortglömd laptop är oläslig utan nyckeln
- *läsning av vissa backups och läckta snapshots* - oanvändbara om krypteringen är på plats och nyckeln inte följt med
- *manipulation av data* - när kryptering kombineras med integritetskontroll eller signering
- *obehörig åtkomst till lagringsmedia* - den gemensamma nämnaren för punkterna ovan

En viktig förutsättning för flera av dessa: nyckel och data måste förvaras separat för att skyddet ska hålla.

### Kryptering skyddar inte mot

Det här är minst lika viktigt, för det är ofta här riktiga incidenter sker. Kryptering kan inte skydda mot ett system som fungerar som tänkt, fast med fel person vid rodret:

- *komprometterade användarkonton* - systemet dekrypterar åt en angripare precis som åt den riktiga användaren
- *felaktiga IAM-rättigheter* - för vida rättigheter ger åtkomst, och krypteringen vet inget om att rättigheterna var för breda
- *applikationsbuggar och SQL-injection* - datan hämtas och returneras via en autentiserad applikation
- *läckta API-nycklar* - ger direkt autentiserad åtkomst, oavsett att datan bakom är krypterad
- *malware på klienten* - kan läsa data i klartext efter att den dekrypterats för visning
- *insiders med rätt behörighet* - en betrodd identitet har per definition rätt att dekryptera
- *loggar med känslig information* - data som krypterats omsorgsfullt kan råka hamna i klartext i en loggfil
- *data som används i klartext i applikationen* - för oss tillbaka till data in use

> Kryptering är ett starkt skydd, men den ersätter inte IAM, loggning, least privilege, säkra applikationer eller incidenthantering. Lagren hänger ihop, och ett starkt skydd i ett lager kompenserar inte för svagheter i ett annat. En välkrypterad databas med felkonfigurerade IAM-rättigheter är inte en säker databas.

---

## Snabbrepetition

| Begrepp | Kort beskrivning |
|---|---|
| Secret | Information som i sig ger åtkomst eller bevisar identitet |
| Symmetrisk kryptering | Samma nyckel krypterar och dekrypterar - snabb, bra för data at rest |
| Asymmetrisk kryptering | Nyckelpar med publik och privat nyckel - löser nyckeldelning |
| Hashning | Envägsfunktion, inget sätt tillbaka - integritet och lösenordslagring |
| Salt | Slumpvärde mot lösenord, stoppar rainbow tables |
| Signering | Hash + privat nyckel = bevisar ursprung och integritet |
| TLS/HTTPS | Krypterar trafik, autentiserar server, skyddar integritet |
| Certifikat | Binder identitet till publik nyckel, signerat av en CA |
| Certifikatkedja | Rot-CA → mellanliggande CA → servercertifikat |
| Data at rest | Lagrad data - skyddas med kryptering |
| Data in transit | Data på väg - skyddas med TLS, mTLS, SSH, VPN |
| Data in use | Data i minne - skyddas med isolering och least privilege |
| KMS | Tjänsten som hanterar nycklar |
| HSM | Hårdvaran som skyddar nycklarna |
| Nyckelrotation | Byt nycklar regelbundet för att begränsa skada |
| BYOK | Vi äger nycklarna, även i molnet |
