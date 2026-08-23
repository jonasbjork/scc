# Begreppslista - Data Protection och Data Lifecycle

> **Var uppmärksam!** Listan är skapad med AI och kan innehålla fel.

**Accessloggar**
: Loggar som registrerar åtkomst till en resurs, till exempel en fil eller ett API. Innehåller typiskt metadata som vem som öppnade resursen, när det hände och från vilken IP-adress. Accessloggar är en av platserna en fil hamnar på utan att vi aktivt bestämt det, och beroende på konfiguration kan de innehålla mer känslig information än vi anar.

**Anonymisering**
: Att ta bort kopplingen mellan data och en specifik individ helt, så att personen inte längre går att identifiera, varken direkt eller indirekt. Skiljer sig från pseudonymisering genom att kopplingen inte går att återställa.

**Arkivering**
: Ett aktivt beslut om att spara data för ett specifikt syfte, under en definierad tid, med tydlig åtkomstkontroll. Arkivering är inte samma sak som att bara låta data ligga kvar, utan kräver att vi kan svara på varför datan sparas, hur länge, vem som får läsa den och hur den till sist ska raderas.

**Backup**
: En säkerhetskopia av ett lagringssystem. I ett dataskyddssammanhang är backup viktigt att komma ihåg som en egen plats där data lever kvar, ofta i ett separat system med egen retention och egen åtkomstkontroll. Att radera originaldata betyder inte att den försvinner ur backupen.

**CDN (Content Delivery Network)**
: Ett nätverk av servrar, geografiskt utspridda, som cachar och levererar innehåll närmare slutanvändaren för att snabba upp åtkomsten. Skapar samtidigt fler platser där kopior av data existerar.

**CIA-modellen**
: De tre grundläggande dimensionerna inom informationssäkerhet: *konfidentialitet*, *integritet* och *tillgänglighet*. I molnmiljöer kompletteras modellen ofta med en fjärde dimension, dataspridning.

**Data at rest**
: Data i lagrat tillstånd, till exempel i en databas, en fil på disk, ett objekt i objektlagring eller ett backuparkiv. Skyddas främst genom kryptering av lagringsmedia, behörighetsstyrning, nyckelhantering och en definierad retention.

**Data in transit**
: Data som skickas mellan system, till exempel från webbläsare till server eller i ett API-anrop till en tredjepartstjänst. Skyddas främst genom TLS, VPN, privata endpoints och korrekt certifikathantering.

**Data in use**
: Data som aktivt behandlas av ett system, till exempel när en databasfråga körs eller ett personnummer visas i ett gränssnitt. Det svåraste tillståndet att skydda, eftersom datan måste vara läsbar för systemet som använder den. Skyddet handlar därför om att minimera exponering snarare än om kryptering, till exempel genom maskning och pseudonymisering.

**Data Lifecycle**
: Datans livscykel, de sju stegen data går igenom från att den skapas till att den raderas: skapa, samla in, lagra, använda, dela, arkivera och radera. Radering är i praktiken det steg som oftast hanteras sämst.

**Dataminimering**
: Principen att inte samla in eller skapa mer data än vad som faktiskt behövs för ett givet syfte. Det mest effektiva dataskyddet som finns, eftersom data som aldrig skapas heller aldrig behöver skyddas, hanteras eller raderas.

**Dataskydd**
: Att säkerställa att information bara ses av rätt personer, inte ändras utan behörighet, finns tillgänglig när den behövs, inte sprids okontrollerat, hanteras enligt gällande lagar och avtal, samt raderas när den inte längre ska finnas kvar. Ett bredare begrepp än bara kryptering eller GDPR-efterlevnad.

**Dataspridning**
: Den fjärde dimensionen av dataskydd, utöver CIA-modellen: var finns kopior av informationen? Extra viktigt i molnmiljöer, eftersom data ofta repliceras, cachas och kopieras automatiskt till fler platser än vi tänkt oss, som en bieffekt av hur molntjänster är designade för tillgänglighet och prestanda.

**GDPR**
: Dataskyddsförordningen (*General Data Protection Regulation*), EU:s lagstiftning som reglerar hur personuppgifter får samlas in, användas, sparas och raderas. Ställer krav som återkommer genom hela datans livscykel, från insamling till radering.

**Integritet**
: Den del av CIA-modellen som handlar om att information är korrekt och oförändrad. Frågan vi ställer är hur vi vet att det vi läser faktiskt är det som ursprungligen skrevs, det vill säga att data inte manipulerats obehörigt.

**Klient-cache**
: En lokal kopia av data som sparas i en webbläsare eller annan klient efter att den laddats ner eller öppnats, till exempel på en anställds egen dator. En plats organisationen normalt inte har någon kontroll över alls.

**KMS (Key Management Service)**
: En tjänst för att hantera krypteringsnycklar, där nyckeln lever separat från datan som krypteras. Gicks igenom i förra kapitlet, och nämns här som exempel på ett tekniskt skydd som kan vara korrekt konfigurerat samtidigt som organisationen ändå saknar kontroll över dataspridningen.

**Konfidentialitet**
: Den del av CIA-modellen som handlar om vem som får se informationen. Byggs upp genom åtkomstkontroll, kryptering och korrekt konfigurerade behörigheter, så att rätt identiteter kan läsa data och fel identiteter inte kan det.

**Maskning**
: En teknik för att dölja delar av ett känsligt värde, till exempel genom att bara visa de fyra sista siffrorna i ett kortnummer. Ett sätt att minska exponering av känslig data i användningsfasen, data in use, utan att behöva kryptera bort hela värdet.

**Primär lagring**
: Det lagringssystem hos en molnleverantör där data i första hand sparas, till exempel objektlagring eller en databas. Utgångspunkten för var en fil finns, men sällan hela sanningen eftersom data ofta även finns i replicas, backup, cache och fler platser samtidigt.

**Pseudonymisering**
: Att ersätta identifierande uppgifter i data med en pseudonym, så att kopplingen till en specifik individ döljs men går att återställa vid behov, till skillnad från anonymisering. Används för att minska exponering av känslig data, till exempel vid felsökning eller analys.

**Radering**
: Det sista steget i datans livscykel, och det som oftast hanteras sämst i praktiken. Kräver att vi vet var all data finns, inklusive kopior i backup, loggar, tredjepartstjänster och exportfiler, samt att vi kan bevisa att raderingen faktiskt skett.

**Replicas**
: Kopior av data som skapas automatiskt i flera tillgänglighetszoner eller geografiska regioner, för att säkerställa tillgänglighet om ett datacenter får problem. Skapar samtidigt fler platser där data existerar, ibland i länder med andra lagar än den ursprungliga regionen.

**Retention**
: Hur länge data ska sparas innan den arkiveras eller raderas. En fråga som ofta aldrig besvaras aktivt när ett system sätts upp, vilket leder till att data sparas på obestämd tid, inte som ett beslut utan som en frånvaro av ett beslut.

**Tillgänglighet**
: Den del av CIA-modellen som handlar om när informationen måste vara åtkomlig. Lätt att glömma bort i säkerhetsdiskussioner, men data som är perfekt skyddad och ändå inte går att komma åt när den behövs tjänar ingenting. Byggs upp genom redundans, backup och disaster recovery.

**Tredjepartstjänst**
: En extern tjänst, till exempel ett analysverktyg eller supportsystem, som organisationen integrerat mot sina egna system. Kan ha indexerat eller kopierat data som en del av sin funktion, och representerar en plats där vi lätt tappar kontroll över dataspridningen.

**Utvecklings- och testmiljö**
: En miljö där produktionsdata ibland kopieras in för att till exempel reproducera en bugg. Har ofta betydligt svagare åtkomstkontroll än produktionsmiljön, och är en av de vanligaste platserna känslig data glöms bort på.
