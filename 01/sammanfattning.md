> Detta dokument är AI-genererat och kan innehålla fel

# Secure Cloud Computing - sammanfattning

Det här är en sammanfattning av kapitlet om säker molndrift. Tanken är att vi ska ha de viktigaste begreppen samlade på ett ställe, som stöd inför tentamen och som något att slå upp i när vi jobbar praktiskt. Vill vi ha hela resonemanget och alla exempel går vi tillbaka till kursmaterialet.

Den bärande tanken i hela kapitlet är enkel, men lätt att missförstå: molnet betyder inte att någon annan tar hand om allt. Molnet betyder att ansvaret flyttas, delas och ibland blir svårare att se. Vi kan delegera driften. Vi kan inte delegera ansvaret.

## Vad är cloud computing?

Cloud computing, eller molntjänster, handlar om att IT-resurser levereras som tjänster över ett nätverk. Istället för att äga och drifta egna servrar hyr vi dem av någon annan och når dem via internet. Det kan vara virtuella servrar, lagring, databaser, utvecklingsplattformar, färdiga applikationer eller AI-tjänster.

Det gamla skämtet att "molnet bara är någon annans dator" stämmer till viss del, men missar det viktigaste. Cloud computing är inte bara en fråga om *var* datorerna står. Det är en modell för hur resurser levereras, skalas, mäts och hur ansvaret fördelas.

*National Institute of Standards and Technology* (*NIST*) har tagit fram en välkänd definition. Enligt den kännetecknas cloud computing av fem egenskaper:

- **On-demand self-service** - vi startar resurser på egen hand, utan att kontakta support.
- **Broad network access** - tjänsterna nås via standardiserade protokoll, oavsett enhet.
- **Resource pooling** - leverantören delar sina fysiska resurser mellan många kunder.
- **Rapid elasticity** - vi skalar upp och ner snabbt, ibland automatiskt.
- **Measured service** - användningen mäts och vi betalar för det vi förbrukar.

Tillsammans är det här det som skiljer riktig molndrift från att bara hyra serverplats i ett datacenter. En *co-location*-lösning uppfyller inte kriterierna. En molntjänst gör det.

> NIST:s definition är inte bara teori. Den används som referens i upphandlingar, avtal och säkerhetspolicys, så det är värt att kunna den. Varje egenskap för också med sig ett säkerhetsansvar - self-service ställer krav på identitetshantering, resursdelning kräver att vi litar på leverantörens isolering, och mätning innebär att all aktivitet loggas.

## Från serverhall till moln

Molnet växte fram steg för steg, där varje steg löste ett problem men skapade nya frågor om ansvar:

- **Egen serverhall** - organisationen äger och ansvarar för allt. Dyrt, kompetenskrävande och svårt att skala.
- **Virtualisering** - flera virtuella servrar på samma fysiska maskin. Bättre resursutnyttjande, men samma ansvar som förut.
- **Hosting och colocation** - någon annan sköter serverhallen, men vi äger ofta hårdvaran och sköter den själva.
- **IaaS** - vi hyr virtuella servrar av en molnleverantör och slipper hårdvaran.
- **PaaS** - vi fokuserar på applikationen, leverantören sköter operativsystemet under.
- **SaaS** - vi använder en färdig applikation och installerar ingenting.

Mönstret är tydligt: ju längre upp i modellen vi rör oss, desto mindre *tekniskt* driftansvar har vi. Men det tekniska ansvaret är inte det enda som finns. Juridiskt ansvar och informationsägarskap försvinner inte för att vi köper mjukvara som tjänst.

## IaaS, PaaS och SaaS

De tre tjänstemodellerna är kärnan i kursen. Det räcker inte att kunna namnen - vi behöver förstå var gränsen för vårt ansvar går i varje modell.

### IaaS - Infrastructure as a Service

Med *IaaS* hyr vi virtuell infrastruktur: server, nätverk, lagring och möjligheten att sätta brandväggsregler (*security groups*). Exempel är AWS EC2, Azure Virtual Machines och Google Compute Engine. Vi får i princip en tom maskin. Den råkar bara stå någon annanstans.

Kunden ansvarar för operativsystemet och dess patchning, applikationer, användare och åtkomst, backup, härdning, loggning och all data. Leverantören ansvarar för fysisk säkerhet, hårdvara, virtualiseringsplattformen och grundläggande nätverk.

> En VM i Azure är inte automatiskt säker bara för att Azure är ett stort företag. Glömmer vi att patcha, konfigurerar brandväggen fel eller lämnar SSH öppet mot internet är det vårt ansvar, inte leverantörens.

### PaaS - Platform as a Service

Med *PaaS* hanterar vi en plattform istället för en server. Vi skriver kod och hanterar data och identiteter, men bryr oss inte om vilket operativsystem som körs under huven. Exempel är Azure App Service, Heroku, Google Cloud Run och Red Hat OpenShift som hanterad plattform.

Kunden fokuserar på kod, konfiguration, data, identiteter, *secrets* och applikationssäkerhet. Leverantören tar ansvar för *runtime*, skalning, tillgänglighet, underliggande operativsystem och delar av patchningen. Ansvaret minskar inte - det förflyttas.

> Kubernetes och OpenShift visar att verkligheten inte alltid passar i strikta fack. Kör vi Kubernetes själva på egna servrar är det IaaS. Använder vi det som en hanterad tjänst blir det mer PaaS-likt. Samma teknik, helt olika ansvarsfördelning.

### SaaS - Software as a Service

Med *SaaS* använder vi en färdig applikation. Vi installerar och patchar ingenting. Microsoft 365, Google Workspace, Dropbox, Salesforce och Slack är typiska exempel.

Det låter enkelt, och det är just det som är problemet. Kunden ansvarar fortfarande för vilka användare som har åtkomst, *Multi-Factor Authentication* (*MFA*), behörighetsnivåer, hur data klassificeras, delningsinställningar, efterlevnad av lagar, incidentrutiner och användarutbildning. Leverantören ansvarar för drift, patchning av plattformen och teknisk tillgänglighet.

> SaaS är lätt att börja använda men svårt att styra. En anställd kan sätta upp ett Dropbox-konto och börja dela filer på femton minuter utan att IT vet om det. Det är ett klassiskt exempel på *Shadow IT* - tjänster som används utan att vara sanktionerade eller granskade. SaaS är just den modell där Shadow IT oftast börjar.

## Public, private och hybrid cloud

Tjänstemodellerna beskriver *vad* som levereras. Nästa fråga är *hur* det levereras - var infrastrukturen finns och vem som delar den.

- **Public cloud** - många kunder delar samma underliggande infrastruktur, och leverantören äger och driver allt. AWS, Azure och Google Cloud är de dominerande, men även Microsoft 365 och Dropbox är publika molntjänster. Prisvärt och skalbart, men väcker frågor om var data lagras, vem som kan komma åt den och hur vi tar ut den igen (*vendor lock-in*).
- **Private cloud** - en miljö dedikerad till en enda organisation, där resurser inte delas med andra. Exempel är egen OpenStack- eller OpenShift-drift. Ger bättre kontroll men mer ansvar.
- **Hybrid cloud** - en kombination av privat och publik infrastruktur. Det är den modell de flesta faktiskt använder, ofta utan att kalla det så.

> Privata moln är inte automatiskt säkrare än publika. Det beror helt på organisationens förmåga att förvalta miljön. En välskött publik tjänst är ofta mer driftsäker och välpatchad än ett privat alternativ utan dedikerade resurser.

> I praktiken är de flesta organisationer hybrida vare sig de planerat det eller inte. Från det ögonblick vi börjar använda Microsoft 365 parallellt med ett lokalt system har vi en hybridmiljö. I hybriden gömmer sig riskerna i gränserna och flödena - särskilt i hur identiteter hanteras och hur händelser *över* gränserna loggas.

## Shared Responsibility Model

Det här är kapitlets viktigaste del ur ett säkerhetsperspektiv. En vanlig missuppfattning är att säkerheten är löst så snart vi valt en etablerad leverantör. Det stämmer - men bara för en *del* av säkerheten, och vilken del beror på tjänstemodellen.

*Shared Responsibility Model* beskriver vem som ansvarar för vad:

| Område | On-prem | IaaS | PaaS | SaaS |
|---|---|---|---|---|
| Fysisk säkerhet | Kund | Leverantör | Leverantör | Leverantör |
| Nätverkets grundinfrastruktur | Kund | Leverantör | Leverantör | Leverantör |
| Virtualisering | Kund | Leverantör | Leverantör | Leverantör |
| Operativsystem | Kund | Kund | Leverantör/plattform | Leverantör |
| Applikation | Kund | Kund | Kund/Delat | Leverantör |
| Data | Kund | Kund | Kund | Kund |
| Identiteter och åtkomst | Kund | Kund | Kund | Kund/Delat |
| Konfiguration | Kund | Kund | Kund | Kund/Delat |
| Efterlevnad och juridiskt ansvar | Kund | Kund | Kund | Kund |

Det viktigaste ser vi längst ner i tabellen: oavsett modell ansvarar vi alltid för vår egen data, våra identiteter och vår efterlevnad av lagar. Det försvinner aldrig.

AWS formulerar samma princip som *security of the cloud* (leverantörens ansvar för infrastrukturen) och *security in the cloud* (kundens ansvar för det som körs inuti). Microsoft uttrycker att ansvaret förskjuts ju mer hanterad tjänsten är, men att kunden alltid behåller ansvaret för data, identiteter och åtkomst.

> Tänk så här: leverantören bygger och säkrar huset. Vi ansvarar för vad vi gör inne i det. Microsoft 365 kan vara en välsäkrad tjänst, men vi kan ändå skapa allvarliga risker genom hur vi konfigurerar den - saknad MFA, publikt delade dokument, gamla konton som inte stängts eller loggning som aldrig sattes upp. Det är inte leverantörens fel. Det är ett konfigurationsansvar som ligger hos oss.

## SLA - Service Level Agreement

Ett *Service Level Agreement* (*SLA*) är leverantörens löfte om vilken tjänstenivå vi kan förvänta oss, framför allt när det gäller tillgänglighet. Men ett SLA är inte en garanti för att verksamheten klarar ett avbrott, och den skillnaden är viktig.

Tillgänglighet mäts i procent, och siffrorna ser bra ut tills vi räknar om dem till faktisk nedtid:

| SLA | Ungefärlig maximal nedtid per år |
|---:|---:|
| 99% | ca 3,65 dagar |
| 99,9% | ca 8,76 timmar |
| 99,99% | ca 52,6 minuter |
| 99,999% | ca 5,26 minuter |

99,9% låter nära perfekt, men innebär att tjänsten får vara nere i nästan nio timmar per år och ändå uppfylla sitt SLA. När vi läser ett SLA bör vi alltid fråga: hur mäts tillgängligheten, vad räknas som avbrott, gäller det hela tjänsten, krävs en viss arkitektur för att det ska gälla, och vad får vi om leverantören bryter mot det?

Ett SLA beskriver vad leverantören lovar - inte vad verksamheten faktiskt klarar. Därför måste arbetet med SLA kopplas till verksamhetens egna krav, det vi kallar *Recovery Time Objective* (*RTO*, hur länge vi kan vara nere) och *Recovery Point Objective* (*RPO*, hur mycket data vi har råd att förlora). De begreppen återkommer när vi pratar om Disaster Recovery.

> En vanlig fallgrop är att tillgänglighetsgarantin bara gäller om vi konfigurerat tjänsten på ett visst sätt, till exempel med redundans över flera tillgänglighetszoner. Har vi inte gjort det gäller inte SLA, oavsett vad produktbladet lovade. Och kompensationen vid brutet SLA - ofta en fakturarabatt - täcker sällan de verkliga kostnaderna för ett avbrott. Ett SLA är ett juridiskt dokument, inte en säkerhetsfunktion.

## Fördelar och risker

Molntjänster kommer med verkliga fördelar, annars hade de inte blivit så vanliga. De kommer också med risker vi behöver kunna hantera.

Bland fördelarna finns att det går snabbt att komma igång, mindre behov av egen hårdvara, god skalbarhet, global tillgänglighet, professionell datacenterdrift, många färdiga säkerhetsfunktioner, goda möjligheter till automatisering via API och att vi betalar för faktisk användning.

Bland riskerna finns mindre direkt kontroll, risk för felkonfiguration, leverantörsberoende, svår kostnadskontroll, juridiska frågor kring var data lagras, Shadow IT, komplex identitetshantering, sämre överblick över var data finns och krav på ny kompetens.

> Det finns en lockelse att tänka "moln är säkrare" eller "moln är osäkrare". Sanningen är mer nyanserad: moln förändrar riskbilden. Vissa risker minskar, andra tillkommer. Vår uppgift är att förstå den förändrade riskbilden - inte att ha en principiell åsikt om huruvida moln är bra eller dåligt. Felkonfiguration är för övrigt en av de vanligaste orsakerna till säkerhetsincidenter i molnet: en publik S3-bucket, en VM med SSH öppet mot hela internet eller ett lagringskonto utan kryptering.

## Vad förändras för oss som IT-säkerhetsspecialister?

Rollen försvinner inte, men tyngdpunkten förskjuts. I en traditionell miljö handlade mycket om det fysiska och tekniska - nätverk, servrar, brandväggar och patchar. Det är fortfarande viktigt, men i molnet räcker det inte.

Vi behöver kunna:

- förstå molnmodellerna och vad vi ansvarar för i var och en
- läsa och ifrågasätta SLA istället för att bara acceptera siffror
- förstå ansvarsfördelningen så att inget faller mellan stolarna
- analysera leverantörsrisk och beroende av en enda leverantör
- granska konfigurationer och hitta det som är fel eller saknas
- förstå identitet och åtkomst, där identitet ofta är den nya gränsen
- identifiera känslig data och veta var den finns
- bedöma risker med dataspridning till fel plats eller fel land
- ställa krav på loggning, backup och incidenthantering
- kommunicera risker till verksamheten på ett begripligt sätt

I traditionell IT ritade vi en yttre gräns och försökte hålla angriparna utanför. I molnet är den gränsen suddig eller saknas helt. Data finns i tjänster utanför vår kontroll, användare når systemen från vilken enhet som helst och externa parter har ofta åtkomst till delar av miljön.

Därför börjar säkerhet i molnet ofta med identitet, data, konfiguration och avtal - inte med serverrummet. Det är inte ett enklare utgångsläge. Det är ett annat, och det kräver att vi är bekväma i gränslandet mellan teknik, juridik, organisation och affärsrisk.

## Snabb repetition inför tentamen

- Molnet flyttar och delar ansvar - det tar inte bort det.
- NIST:s fem egenskaper skiljer riktig molndrift från vanlig virtualisering eller colocation.
- IaaS, PaaS och SaaS - ju högre upp, desto mindre *tekniskt* driftansvar, men data, identiteter och efterlevnad är alltid vårt.
- Public, private och hybrid beskriver *hur* tjänsten levereras. De flesta är hybrida i praktiken.
- Shared Responsibility Model är tankesättet vi återkommer till genom hela kursen.
- Ett SLA lovar tillgänglighet men säger inget om vad ett avbrott kostar oss. Koppla alltid till RTO och RPO.
- Moln är inte säkrare eller osäkrare - riskbilden förändras.
