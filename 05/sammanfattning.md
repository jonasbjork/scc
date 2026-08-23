# Sammanfattning: Data Protection och Data Lifecycle

> Detta dokument är AI-genererat och kan innehålla fel.

Det här är en sammanfattning av kapitlet om Data Protection och Data Lifecycle. Använd den gärna som repetition inför en quiz eller ett prov, eller som uppslagsverk när ni jobbar med övningarna.

## Kryptering räcker inte

I förra kapitlet gick vi igenom kryptering och secrets management: hur vi skyddar data tekniskt när den lagras, transporteras och används. Det är viktig kunskap, men det löser bara en del av problemet.

Tänk er en organisation som krypterar all sin data i molnet, med starka nycklar, en välkonfigurerad KMS och TLS överallt. Ändå ligger personuppgifter från ett gammalt projekt kvar i ett system ingen längre tittar på, loggar innehåller kunddata som borde ha raderats, en backup har replikerats till fel region, och en tredjepartstjänst har åtkomst ingen minns varför den har.

Data är krypterad. Men är den skyddad?

Dataskydd i molnet är inte ett tekniskt problem vi löser en gång. Det är en kontinuerlig process som sträcker sig över hela datans livscykel, från att den skapas till att den raderas på ett kontrollerat sätt. För att klara det behöver vi kunna svara på grundläggande frågor om vår data: vilken information har vi, var finns den, vem har åtkomst, hur länge ska den sparas, hur raderar vi den på ett sätt vi kan verifiera, och vilka risker uppstår när den sprids mellan system, backupper, loggar och tredjepartstjänster?

## Varför dataskydd i molnet är svårt

I en traditionell miljö kan vi peka på var en fil finns: den ligger på en server, servern står i ett låst serverrum. I molnet finns inget lika enkelt svar, och det är just den otydligheten som gör dataskydd svårt.

En fil som laddas upp till en molntjänst hamnar sällan bara på ett ställe. Den finns typiskt på flera av de här platserna samtidigt, utan att någon aktivt bestämt det:

- **primär lagring**, där filen egentligen "hör hemma"
- **replicas** i flera tillgänglighetszoner och ibland flera geografiska regioner, för att säkerställa tillgänglighet
- **accessloggar**, som registrerar vem som öppnat filen, när och varifrån
- **backup**, ofta i ett separat system med egen retention och egen åtkomstkontroll
- **cache och CDN-noder**, för att snabba upp leveransen till användare
- **tredjepartstjänster**, som analysverktyg eller supportsystem, som kan ha indexerat eller kopierat innehållet
- **utvecklings- och testmiljöer**, ofta med svagare åtkomstkontroll än produktion
- **klient-cache**, lokalt på en dator organisationen inte har kontroll över

Vi kan inte skydda data vi inte vet att vi har, vi kan inte radera data vi inte vet var den finns, och vi kan inte kontrollera åtkomst till data vi inte vet att vi delar. Det är utgångspunkten för resten av kapitlet.

## Vad betyder dataskydd?

Dataskydd är ett ord som används löst i branschen, ibland som synonym för kryptering, ibland för GDPR. Mer precist handlar det om att säkerställa att information bara ses av rätt personer, inte ändras utan behörighet, finns tillgänglig när den behövs, inte sprids okontrollerat, hanteras enligt gällande lagar och avtal, och raderas när den inte längre behövs.

Det byggs på fyra dimensioner:

| Dimension | Frågan vi ställer |
|---|---|
| Konfidentialitet | Vem får se informationen? |
| Integritet | Hur vet vi att informationen är korrekt och oförändrad? |
| Tillgänglighet | När måste informationen vara åtkomlig? |
| Dataspridning | Var finns kopior av informationen? |

De tre första känner vi igen som CIA-modellen från grundläggande informationssäkerhet. Den fjärde, dataspridning, är extra viktig i molnmiljöer och minst lika central som konfidentialitet, trots att den ofta glöms bort. En fil kan vara krypterad, åtkomstkontrollerad och integritetsverifierad, och ändå vara ett problem om den replikerats till fel land, sparas i för många kopior, eller lever kvar längre än nödvändigt. Dataskydd kräver att vi ställer alla fyra frågorna, inte bara den om konfidentialitet.

## Data Lifecycle: datans sju steg

All data har en livscykel: den skapas, samlas in, lagras, används, delas, arkiveras och till sist, i bästa fall, raderas. Det sista steget är det som oftast missas i praktiken.

**Skapa.** Data börjar sin livscykel när den skapas, av en användare, ett system eller en process. De viktigaste frågorna är de enklaste: behöver vi verkligen skapa den här datan? *Dataminimering*, att inte samla in mer än nödvändigt, är det mest effektiva dataskyddet som finns. Data som aldrig skapas behöver varken skyddas, hanteras eller raderas.

**Samla in.** När data hämtas in från en yttre källa frågar vi oss om vi har rätt att samla in den, om användaren informerats, och om den innehåller personuppgifter med specifika krav enligt GDPR. Insamling utan tydligt syfte är både ett juridiskt och ett säkerhetsproblem.

**Lagra.** Var lagras datan, är den krypterad, vem har åtkomst, och vilken *retention* gäller, det vill säga hur länge vi vill spara den? Retention är en fråga som ofta aldrig besvaras aktivt, vilket leder till att data sparas på obestämd tid, inte som ett beslut utan som en frånvaro av ett beslut.

**Använda.** Data som lagras används förr eller senare. Vi frågar oss vem som använder den och om de verkligen behöver fullständig data. Genom *pseudonymisering*, att ersätta identifierande uppgifter med en pseudonym, eller *anonymisering*, att ta bort kopplingen helt, kan vi minska exponeringen även i användningsfasen.

**Dela.** Data stannar sällan i ett system. Varje delning, till en tredjepartsleverantör, i en export eller i ett mejl, är en potentiell förlust av kontroll. Vi frågar oss vem datan delas med, vilket avtal som reglerar det, vilket land den hamnar i, och om vi kan begränsa delningen till bara det som behövs.

**Arkivera.** Viss data behöver sparas länge av juridiska eller affärsmässiga skäl. Arkivering ska vara ett aktivt beslut, med tydligt syfte, definierad tid och tydlig åtkomstkontroll, inte att bara låta data ligga kvar.

**Radera.** Det sista och svåraste steget. Att radera primärdata räcker inte om data fortfarande finns kvar i backup, loggar, hos en tredjepartstjänst eller i en gammal exportfil. Radering kräver att vi vet var all data finns, vilket i sin tur kräver kontroll över hela livscykeln från början. Och lika viktigt som själva raderingen: kan vi bevisa att den skett?

## De tre tillstånden, i sammanhanget

Vi känner igen *data at rest*, *data in transit* och *data in use* från tidigare i kursen. Kopplat till livscykeln hör de ihop så här:

**Data at rest** är data i lagrat tillstånd, kopplat till stegen lagra och arkivera. Skyddet bygger på kryptering, behörighetsstyrning, nyckelhantering via en KMS och en definierad retention.

**Data in transit** är data som skickas mellan system, kopplat till delningssteget. Skyddet bygger på TLS, VPN, privata endpoints och korrekt certifikathantering.

**Data in use** är data som aktivt behandlas, kopplat till användningssteget. Det svåraste tillståndet att skydda, eftersom data måste vara läsbar för systemet som använder den. Skyddet handlar därför om att minimera exponering snarare än att kryptera: minsta möjliga åtkomst, *maskning* som bara visar delar av ett värde, och pseudonymisering.

## Den röda tråden

Kapitlet har en enda röd tråd genom alla avsnitt: vi kan inte skydda, kontrollera eller radera data vi inte vet att vi har. Stark kryptering och en välkonfigurerad KMS är nödvändiga, men inte tillräckliga. Dataskydd i molnet kräver att vi känner till vår data genom hela dess livscykel, från det att den skapas till det att den, på ett sätt vi kan bevisa, till sist raderas.
