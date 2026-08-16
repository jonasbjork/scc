# Sammanfattning: IAM, Authorization & Zero Trust

> Detta dokument är AI-genererat och kan innehålla fel

Den här sammanfattningen går igenom de viktigaste begreppen från kapitlet om identitets- och åtkomsthantering i molnet. Den är tänkt som ett stöd inför tentan och som något att slå upp i när vi behöver friska upp minnet. Vill vi ha hela bilden går vi tillbaka till kapitlet, här tar vi det som är viktigast att kunna.

## Varför IAM är centralt i molnet

När vi flyttar ut i molnet försvinner den fysiska gränsen som förr skyddade oss. Servrarna står inte längre i ett eget serverrum bakom en egen brandvägg, utan hos en leverantör och nås över internet. Då räcker inte platsen som säkerhetsgräns längre.

Istället blir *identiteten* den nya gränsen. Det är kontot och dess behörigheter som avgör vem som kommer in och vad de får göra, inte var de befinner sig. Det är därför *Identity and Access Management*, förkortat *IAM*, är så centralt. En felkonfigurerad IAM-policy kan ge en angripare direkt tillgång till precis det vi försöker skydda.

IAM försöker svara på fyra frågor:

- Vem är identiteten? Människa, system eller tjänst?
- Vilken resurs vill identiteten komma åt?
- Vilken åtgärd vill identiteten utföra?
- Under vilka villkor ska det tillåtas?

Frågorna är desamma oavsett om vi jobbar i AWS, Azure eller Google Cloud. Det är principerna vi lär oss, inte en enskild leverantörs syntax.

## Authentication och Authorization

Fyra begrepp hänger ihop men gör olika saker, och de är lätta att blanda ihop:

- *Identifikation* - vem påstår du att du är? Vi anger ett användarnamn eller en e-postadress.
- *Autentisering* (*authentication*) - kan du bevisa det? Med lösenord, certifikat, engångskod, hårdvarutoken eller biometri.
- *Auktorisering* (*authorization*) - vad får du göra? Att vara inloggad ger inte automatiskt tillgång till allt.
- *Granskning* (*auditing*) - vad gjorde du? Loggning så att vi kan utreda incidenter och visa att vi följer regelverken.

> I säkerhetssammanhang ser vi ofta förkortningen *AAA* - *Authentication, Authorization* och *Accounting* (ibland *Auditing*). Det är samma koncept samlat i ett begrepp.

En viktig poäng är att autentisering och auktorisering kan misslyckas var för sig. En angripare som stjäl ett lösenord klarar autentiseringen, men med rätt konfigurerad auktorisering begränsas skadan till vad just det kontot får göra. Det är ett av de starkaste argumenten för *least privilege*.

## IAM-byggstenar

Namnen skiljer sig lite mellan plattformarna, men koncepten är desamma överallt.

*Användare*
: En namngiven identitet kopplad till en specifik person. Varje person ska ha sitt eget konto - delade konton slår ut spårbarheten.

*Grupper*
: Ett sätt att samla användare och tilldela behörigheter till många på en gång, istället för konto för konto.

*Roller*
: En identitet utan fast ägare, som tilldelas tillfälligt för ett specifikt uppdrag. Tänk på det som en arbetsuniform vi tar på oss och lämnar igen. Centralt för att ge tjänster och system tillfällig åtkomst.

*Policies*
: Regeluppsättningen som kopplar ihop identiteter med behörigheter och svarar på vilken resurs, vilken åtgärd och under vilka villkor.

*Permissions*
: De enskilda rättigheterna en policy beviljar eller nekar, från ingen åtkomst alls upp till full administratörskontroll.

*Service accounts*
: Konton som tillhör ett system eller en applikation istället för en människa. Ofta farligare än vanliga konton, eftersom de har permanent åtkomst, sällan använder MFA och är svårare att övervaka.

*API-tokens*
: En lång, slumpmässig sträng som tjänster använder för att autentisera sig istället för lösenord. Kan begränsas i behörighet och få ett utgångsdatum.

> Tänk på att även läsrättigheter kan vara känsliga. Att kunna läsa kunddata, fakturor, loggar eller säkerhetskopior kan vara allvarligt även utan en enda skrivbehörighet.

## Modeller för åtkomstkontroll

När vi vet vilka byggstenar som finns måste vi bestämma *hur* vi kopplar ihop identiteter med behörigheter. De två vanligaste modellerna är RBAC och ABAC.

*RBAC* (*Role-Based Access Control*) kopplar behörigheter till roller, och roller till användare. En ny utvecklare läggs till i rollen "utvecklare" och får automatiskt rätt åtkomst, varken mer eller mindre. Enkelt att administrera och lätt att granska.

*ABAC* (*Attribute-Based Access Control*) tar hänsyn till en samling *attribut* - egenskaper hos identiteten, resursen, åtgärden och omgivningen - och väger in dem tillsammans. Det ger mycket finare kontroll, men kräver mer arbete att designa och underhålla.

| | RBAC | ABAC |
|---|---|---|
| Grundprincip | Behörigheter kopplas till roller | Behörigheter avgörs av attribut |
| Komplexitet | Låg till måttlig | Hög |
| Flexibilitet | Begränsad | Mycket hög |
| Granskbarhet | Hög - enkel att förstå | Lägre - många villkor |
| Passar bra för | Tydliga, stabila roller | Dynamiska miljöer |

RBAC passar när rollerna är tydliga och stabila. ABAC passar när kontexten spelar roll - tid, plats, enhetsstatus eller resursklassificering. I praktiken kombinerar många organisationer modellerna: RBAC som grund, med attributbaserade villkor där vi behöver finare kontroll.

## Least Privilege

*Least privilege* är principen om minsta möjliga behörighet: ge en identitet exakt de rättigheter den behöver för sitt uppdrag och inte ett uns mer. Den låter självklar men görs ofta fel. Vi kan tänka på den i fyra delar:

- **Minimera rättigheter** - fråga "vad behöver den här personen just nu?" istället för "vad kan hon möjligen behöva?"
- **Minimera tid** - ge åtkomst så länge den faktiskt behövs. Moderna plattformar stöder just-in-time-åtkomst som beviljas tillfälligt och sedan försvinner automatiskt.
- **Minimera omfattning** - åtkomsten ska vara snäv i *var* den gäller. En nyckel till ett rum, inte en huvudnyckel till hela byggnaden.
- **Minimera antal permanenta adminkonton** - färre adminkonton är både mindre angreppsyta och lättare att övervaka.

En viktig del är att separera vanliga konton och adminkonton. En administratör har ett vanligt konto för e-post och webb, och ett separat adminkonto som bara används för administrativa uppgifter.

> Kom ihåg att läsrättigheter inte är ofarliga. Att kunna läsa lönelistor, säkerhetsloggar eller krypteringsnycklar kan vara minst lika allvarligt som att kunna radera dem. "Read-only" är inte detsamma som "ofarlig åtkomst".

## MFA, SSO och federering

Lösenordet är den äldsta formen av autentisering och det börjar visa sin ålder. Lösenord stjäls via phishing, läcker vid dataintrång, gissas, delas och hittas i kod. Det gemensamma är att angriparen aldrig behövde bryta sig in, hon fick bara tag på rätt sträng tecken.

*Multi-Factor Authentication* (*MFA*) kombinerar minst två olika typer av bevis:

- Något vi vet - ett lösenord eller en PIN-kod
- Något vi har - en telefon eller en hårdvarutoken
- Något vi är - ett fingeravtryck eller ansiktsigenkänning

MFA är idag den enskilt mest effektiva åtgärden för att skydda konton. Olika metoder är olika starka: SMS-koder är bättre än ingenting men kan kringgås, en autentiseringsapp är bättre, och en hårdvarutoken som en YubiKey är starkast.

*Single Sign-On* (*SSO*) låter användaren logga in en gång och sedan nå alla anslutna tjänster. Det förenklar vardagen och centraliserar administrationen, men samlar också en koncentrerad risk - den som komprometterar SSO-kontot kan nå allt. Därför är MFA på SSO-kontot särskilt viktigt.

*Federation* innebär att vi skapar ett förtroende mellan organisationens eget identitetssystem (ofta *Active Directory* eller *Entra ID*) och molntjänsten via protokoll som *SAML* och *OpenID Connect*. Användaren loggar in med sitt vanliga konto, inga dubbla konton att hålla synkroniserade. Det är samma mönster som "Logga in med Google" eller "Logga in med Microsoft".

> Tänk på SSO-kontot som en huvudnyckel. En huvudnyckel är praktisk, men förlusten av den är katastrofal. Vi skyddar den därefter.

## Service accounts och API-tokens

I en modern molnmiljö är det långt ifrån bara människor som autentiserar sig. Maskinidentiteter - CI/CD-pipelines, webbapplikationer, backupscript, Kubernetes-poddar, övervakningssystem - behöver också bevisa vilka de är, men de kan inte skriva ett lösenord eller godkänna en MFA-popup. Här kommer service accounts och API-tokens in.

Problemet börjar ofta med *långlivade credentials*: en API-token utan utgångsdatum, ett service account-lösenord som aldrig roteras, ett certifikat som gäller i tio år. De är bekväma, men en credential som aldrig går ut ger en angripare obegränsad tid om den läcker.

*Läckta tokens* är ett av de vanligaste problemen. En token hårdkodas i ett testskript och checkas in i ett gitrepo, hamnar i en loggfil, ett felmeddelande eller en Slack-kanal. Det finns verktyg som kontinuerligt söker publika repon efter tokens, och tiden från läcka till missbruk mäts ofta i minuter.

Vi minskar risken på två sätt: **rotera** credentials regelbundet, eller ännu hellre använda kortlivade tokens som upphör automatiskt, och **begränsa** vad de får göra och varifrån de får anropa - least privilege gäller lika mycket för maskiner som för människor. Hemliga värden ska aldrig lagras i kod, utan i en dedikerad *secrets manager*.

> En token som läcker är inte som ett lösenord som läcker. En token kräver ingen ytterligare autentisering. Den är i sig själv beviset. Den som har tokenen kan agera som tjänsten, utan MFA och utan varningar.

## Zero Trust och Conditional Access

*Zero Trust* är inte en produkt vi köper, utan ett säkerhetstänk. Grundtanken är att vi inte automatiskt litar på nätverket, platsen eller en gammal inloggning. Varje åtkomst ska kunna motiveras, kontrolleras och begränsas - varje gång.

Det gamla tänket byggde på en tydlig gräns: innanför brandväggen litade vi på allt, utanför litade vi på inget. Den modellen håller inte längre när resurserna ligger i molnet, anställda jobbar hemifrån och angripare kan ta sig in via phishing eller ett komprometterat konto. Zero Trust vilar på tre principer:

- **Verify explicitly** - verifiera alltid utifrån faktiska bevis: vem är identiteten, vilken enhet används, varifrån kommer anropet, hur färsk är sessionen. Och vi gör det kontinuerligt, inte bara vid inloggning.
- **Least privilege** - samma princip som tidigare, men här som en grundförutsättning. Tillfällig åtkomst framför permanent, låg behörighet framför hög.
- **Assume breach** - utgå från att ett intrång redan skett, och designa systemen för att begränsa skadan genom segmentering, detektering och snabb respons.

Zero Trust realiseras i praktiken via *Conditional Access*, där åtkomstbeslut fattas dynamiskt utifrån kontext - identitet, enhet, plats, tid, beteende och resursens känslighet. Systemet kan då välja att tillåta, kräva MFA igen, begränsa vad som får göras, eller neka och larma.

Ett tydligt exempel: en analytiker loggar in från sin managerade arbetsdator på kontoret en tisdag förmiddag och släpps in utan friktion. Samma analytiker försöker senare logga in från ett okänt nätverk i ett annat land en söndagsnatt och nekas eller möts av ytterligare verifiering. Identiteten och lösenordet är desamma - men kontexten är annorlunda, och det räcker för att systemet ska reagera.

## Vanliga misstag

De här dyker upp gång på gång i granskningar och incidentutredningar. Sällan av slarv, oftare av bekvämlighet eller tidsbrist.

- **Delade konton** - flera personer med samma inloggning slår ut all spårbarhet. Vi vet inte vem som gjorde vad. Varje person ska ha sin egen identitet.
- **Permanenta adminrättigheter** - ett konto som är administratör dygnet runt är ett attraktivt mål dygnet runt. Just-in-time-åtkomst är lösningen.
- **Avsaknad av MFA** - fortfarande vanligt, särskilt på admin- och servicekonton. Ett konto utan MFA kan tas över när som helst.
- **Gamla API-nycklar** - nycklar som skapades för avslutade projekt och aldrig återkallades lever kvar och fungerar. Ett osynligt problem som kräver regelbunden inventering.
- **Servicekonton med för höga behörigheter** - en CI/CD-pipeline med full kontroll, en övervakningsagent med skrivrättigheter överallt. Servicekonton ska följa least privilege, gärna striktare än mänskliga konton.
- **Svaga lösenord** - enkla, återanvända eller aldrig bytta lösenord. Svaret är en lösenordshanterare plus MFA, och kontroll mot kända läckta lösenordsdatabaser.
- **För vida roller och policies** - rättigheter samlas på hög över tid, ett fenomen som kallas *privilege creep*. Kräver regelbunden granskning.

> Om en organisation bara kan prioritera en enda säkerhetsåtgärd för sina konton är svaret alltid MFA. Konsekvent, på alla konton, utan undantag.

En bra fråga att ställa regelbundet: om vi skapade våra roller från scratch idag, med det vi vet nu - skulle de då se ut som de gör? Svaret är nästan aldrig ja, och det är just därför vi granskar.
