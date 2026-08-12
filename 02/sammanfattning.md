# Hot och risker i molnet – sammanfattning

En sammanfattning av dagens föreläsning, med fokus på det viktigaste att kunna inför laborationerna och tentamen.

> Sammanfattningen är automatiskt skapad med AI och kan innehålla fel!

## Del 1: Genomgång av molnplattformar (AWS & Azure)

- Regioner spelar roll. Alla regioner stöder inte samma tjänster – nya funktioner släpps ofta i amerikanska regioner först.
- **Compute** = beräkningskapacitet. Exempel på tjänster:
  - **EC2** (Elastic Compute Cloud) – en virtuell maskin (instans) du själv sätter upp, inklusive nätverk.
  - **Lambda** – serverless computing / Function as a Service (FaaS). Du lägger bara kod, ingen server. Koden triggas av ett event och du betalar bara för de sekunder koden faktiskt kör.
  - **Lightsail** – enkel virtuell maskin, bra nybörjartjänst.
- När man skapar en EC2-instans väljer man bland annat:
  - Operativsystem (t.ex. Ubuntu, Amazon Linux, Windows, RHEL)
  - Arkitektur: **x86** (Intel, vanligast för PC) eller **ARM** (resurseffektivare, bl.a. nya Mac-datorer och mobiler)
  - Instanstyp (mall för CPU/minne) – du faktureras per timme
- **Security Group** = brandväggen i molnet. Styr vilken trafik/IP som får ansluta på vilka portar.
  - Regel att komma ihåg: välj **My IP**, aldrig **Anywhere (0.0.0.0/0)**, när du öppnar SSH mot en ny server. Öppnar du för alla IP-adresser hittar angripare servern på minuter.
- Webbgränssnitt och kommandoradsverktyg (CLI) pratar mot samma API – bara olika sätt att visualisera samma tjänst.
- Andra tjänsteexempel: Route 53 (DNS), VPC (isolerat virtuellt nätverk), CloudFront (CDN – gör webbplatser snabbare genom att sprida ut innehåll globalt), containers/Kubernetes, databaser (RDS, Aurora, DynamoDB), IAM (identitet och åtkomst).
- Infrastruktur i större miljöer byggs ofta med **Terraform** eller **OpenTofu** (Infrastructure as Code) istället för att klicka manuellt.
- Alla molnplattformar (AWS, Azure, Google Cloud, DigitalOcean) fungerar principiellt likadant, bara olika gränssnitt. **DigitalOcean** är enklast att börja med.
- Kom ihåg: allt i molnet kostar pengar – stäng (terminera) resurser du inte längre använder.

## Del 2: Hot och risker i molnet

### Grundtanken

- Att flytta till molnet tar inte bort säkerhetsproblemen – de flyttar bara, och ibland förändras var ansvaret ligger.
- Vanlig missuppfattning: "vi är i molnet nu, så är allt säkert." Fel.

### Ansvarsfördelning (Shared Responsibility Model)

- **Molnleverantören** ansvarar för: fysisk säkerhet i datacenter, grundläggande plattform, delar av nätverk/OS.
- **Du som kund** ansvarar fortfarande för: identiteter, konton, behörigheter, hur tjänsten konfigureras och används.
- Exempel: skapar du ett användarkonto åt en anställd är det ditt ansvar – inte molnleverantörens.

### De vanligaste säkerhetsproblemen i molnet

1. **Felkonfiguration** – vanligast av alla. Ett litet felaktigt val i ett webbgränssnitt kan öppna hela systemet.
2. **Brister i IAM** (Identity and Access Management) – fel eller för breda behörigheter för användare och tjänstekonton.
3. **Osäkra API:er** – data läcker ut för att API:er inte skyddats lika hårt som webbgränssnittet.
4. **Oavsiktlig dataexponering** – t.ex. delade länkar ("alla med länken kan se") eller publika lagringsytor.
5. **Tredjepartsrisker** – supply chain-attacker (exempel: Kaseya → Coop).
6. **Bristande molnstrategi** – man har inte tänkt igenom hur/varför man använder tjänsterna, eller hur man tar sig därifrån.

### Attackytor – från nätverk till identitet

- **Traditionellt synsätt**: nätverket är gränsen. Allt utanför = farligt, allt innanför = pålitligt. Skydd byggdes med brandväggar och portfiltrering (Stateful Package Inspection).
- **I molnet**: plattformen är publikt tillgänglig – man kan inte längre lita på "innanför nätverket". Fokus flyttas till **vem får göra vad** (behörigheter).
- **Identiteten är molnets nya säkerhetsgräns.** Skydda:
  - Användarkonton och administratörskonton (kräver MFA)
  - API-nycklar och access tokens (får aldrig hamna i Git-repon)
  - CI/CD-pipelines
- Viktigt: **TLS/HTTPS skyddar bara data under överföring** – det skyddar inte mot intrång, virus eller missbruk av funktionalitet. Ett hänglås i webbläsaren betyder inte att tjänsten är säker.
- På samma sätt: **kryptering av disk/filer skyddar mot stöld av utrustning**, inte mot intrång i ett system där du redan är inloggad.

### CIA-triaden i molnet

**Konfidentialitet – vem kan läsa data?**
- Vanliga orsaker till läckage: felaktig delning (delade länkar), svaga lösenord, publika lagringsytor, för breda behörigheter, glömda gästkonton, privata enheter med företagsdata.
- Grundregel: utgå från att allt är stängt – fråga sen vem som faktiskt behöver åtkomst.

**Integritet – vem kan ändra data?**
- Risker: manipulerad kod (t.ex. XZ-attacken mot ett Linux-kärnbibliotek), manipulerade container-images, felaktig automatisering, bristande versionshantering och spårbarhet.
- Motåtgärder: logga alla ändringar, använd Git/GitOps, signera kod och artefakter (SSH/GPG-nycklar), begränsa skrivbehörigheter, testa återställning regelbundet.
- **"En backup som aldrig testats för återställning är bara en förhoppning."**

**Tillgänglighet – kan verksamheten använda tjänsten?**
- Risker: leverantörsavbrott, internetavbrott, godtycklig **kontolåsning** från leverantören (kan förstöra hela verksamheten om all data ligger där), **DDoS**-attacker (för många förfrågningar samtidigt gör tjänsten otillgänglig – kan även ske av misstag, t.ex. ett massutskick).
- Fråga dig: Hur viktig är tjänsten? Får den gå ner? Hur länge? Finns reservlösning – och är den testad i praktiken?

### Var finns egentligen vår data?

- Ett enda dokument kan snabbt finnas på 8–9 olika ställen: original, molnlagring, laptop, mobil, mejl till kollega, backup, loggar, testmiljö, AI-verktyg, tredjepartsintegrationer.
- Var extra försiktig med att ladda upp känslig/hemlig data till publika AI-tjänster – det kan användas för modellträning om inget företagsavtal finns.

### Multitenancy och "Noisy Neighbor"

- Molnleverantörer delar samma fysiska hårdvara mellan kunder, men isolerar med virtualisering, identiteter och nätverksregler.
- **Noisy Neighbor**: en annan kund på samma hårdvara drar mycket resurser (CPU/minne/nätverk) och påverkar din prestanda. Kan bli ett prestanda-, säkerhets- och avtalsproblem.
- Extremt känslig data (t.ex. försvarshemligheter) bör inte dela miljö med andra – välj dedikerad hårdvara.

### Shadow IT

- Uppstår när godkända verktyg är för trögjobbade/byråkratiska, och anställda löser problemet själva (t.ex. privat Dropbox, Trello, GitHub-repo).
- Det handlar sällan om illvilja – människor vill bara få jobbet gjort och väljer den enklaste vägen.
- **Shadow IT är ofta ett symptom på att de godkända verktygen inte möter verksamhetens behov.**

### Leverantörsberoende och vendor lock-in

- Innan ni börjar använda en tjänst: hur får ni ut er data? I vilket format? Vad kostar det att avsluta?
- Vendor lock-in är inte alltid negativt – men ni måste vara medvetna om risken och acceptera den aktivt.
- Ha alltid en **exit-strategi** klar innan ni behöver den.

## De fyra scenarierna (lär in dessa – kommer i labbarna)

1. **Publik lagringsyta av misstag** → data blir läsbar för hela internet. Motåtgärd: blockera publik åtkomst som standard, automatiska policykontroller och larm.
2. **Läckt API-nyckel** (t.ex. i ett Git-repo) → angripare kan starta hundratals instanser (cryptomining) på ditt konto och du får fakturan. Motåtgärd: använd Secrets Manager (t.ex. HashiCorp Vault), aldrig nycklar i källkod, rotera nycklar. OBS: att bara ta bort nyckeln från senaste filversionen räcker inte – den finns kvar i Git-historiken.
3. **För höga behörigheter** → en administratörsanvändare blir nätfiskad (phishing), angriparen får full åtkomst. Motåtgärd: **least privilege** – minsta möjliga behörighet, på minsta möjliga område, under kortast möjliga tid.
4. **Bristande loggning** → man vet att något hänt men inte vad, när eller hur mycket skada. Utredningen blir lång och osäker. Motåtgärd: aktivera och skydda loggning (central loggserver, separat från de övervakade systemen, så loggar inte kan raderas av en angripare).

Gemensamt för alla fyra: **ingen avancerad attack krävdes** – det var egna felaktiga val i konfigurationen som orsakade skadan.

## Grundprinciper för säker molnanvändning

- Aktivera **MFA**
- Följ **least privilege**
- Blockera publik åtkomst som standard
- Hantera hemligheter separat (Secrets Manager)
- Aktivera och skydda loggning (central, isolerad loggserver)
- Inventera resurser och integrationer, granska externa/gäst-konton
- Testa backup och återställning i praktiken
- Skapa incidentplan **innan** en incident inträffar
- Skapa exit-strategi **innan** ni behöver lämna en leverantör

## Sammanfattning i en mening

Molnet ger mindre kontroll men samma ansvar. Identitet och behörigheter är den nya säkerhetsgränsen, data sprids till fler platser än ni tror, och de flesta incidenter orsakas av egna felkonfigurationer – inte avancerade angripare.

## Dagens laborationer (Docker-baserade)

- **Trivy** – sårbarhetsskanning av container-images (jämför t.ex. `python:3.9` mot `python:3.12-slim`)
- Ansvarsgräns IaaS/PaaS/SaaS kopplat till en databastjänst
- **MinIO** – skapa en publik bucket av misstag och se konsekvensen
- **GitLeaks** – hitta läckta API-nycklar i ett Git-repo (och se att de finns kvar i historiken även efter att filen ändrats)
- **Keycloak** – sätta upp MFA och uppleva inloggning med/utan det
- Docker Compose-scenario – hitta minst fem uppenbara säkerhetsbrister på egen hand

Material finns här i gitrepot. Ni har resten av dagen samt torsdag och fredag på er.
