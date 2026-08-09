# Secure Cloud Computing<br/>Övningar - Lektion 1

## Övning 1 - Är det verkligen ett moln?

En uppvärmning som tränar NIST:s fem egenskaper. Poängen är inte att lära sig en definition utantill, utan att kunna använda den för att skilja en riktig molntjänst från något som bara ser ut som ett moln.

**Efter övningen ska du kunna:**

- använda NIST:s fem egenskaper som ett praktiskt verktyg
- avgöra om en tjänst faktiskt är cloud computing eller något annat

### Uppgift

Ni får ett antal beskrivningar. För varje beskrivning ska ni svara på:

1. Är det här cloud computing? Ja, nej eller gråzon.
2. Vilka av NIST:s fem egenskaper är uppfyllda, och vilka saknas?

De fem egenskaperna, som repetition:

- on-demand self-service
- nätverksåtkomst (*broad network access*)
- resursdelning (*resource pooling*)
- snabb skalbarhet (*rapid elasticity*)
- mätbara tjänster (*measured service*)

Beskrivningar att bedöma:

1. En organisation hyr rackplats i ett externt datacenter och ställer dit sina egna fysiska servrar. Personalen åker dit för att installera hårdvaran.
2. En utvecklare startar en virtuell server i AWS via en webbportal, använder den i tre timmar och stänger sedan av den. Fakturan visar exakt antal använda timmar.
3. IT-avdelningen har en VMware-miljö. När någon behöver en ny server skickar hon in en beställning via e-post och en tekniker skapar den manuellt inom några dagar.
4. Ett företag använder Microsoft 365 för e-post och dokument. Kontona skapas via en självserviceportal och tjänsten nås från valfri enhet.
5. En intern OpenStack-plattform där utvecklarna själva startar och stänger resurser via ett API, med automatisk skalning och månadsvis intern kostnadsredovisning per avdelning.

> **Tips till diskussionen:** Fråga er inte bara *är det ett moln?* utan *vad är det som saknas för att det ska bli ett moln?*. Det är i gränsfallen som förståelsen sitter.

## Övning 2 - Vems ansvar?

Den här övningen tränar *Shared Responsibility Model* som är lektionens viktigaste del. Vi drillar den snabbt och konkret innan vi använder den i de större övningarna.

**Efter övningen ska du kunna:**

- avgöra om ett säkerhetsproblem är kundens eller leverantörens ansvar
- koppla ansvaret till rätt tjänstemodell (IaaS, PaaS eller SaaS)

### Uppgift

För varje händelse nedan ska ni svara på två frågor:

1. Vems ansvar är det här? Kundens eller leverantörens?
2. I vilken eller vilka tjänstemodeller uppstår ansvaret? IaaS, PaaS, SaaS eller flera?

Händelser:

1. En lagringsyta (till exempel en S3-bucket) sätts av misstag som publik och känsliga filer blir läsbara för vem som helst.
2. Ett strömavbrott i leverantörens datacenter tar ner en hel region i några timmar.
3. MFA är inte påslaget i Microsoft 365. Ett läckt lösenord räcker för att ta över ett konto.
4. En Azure-VM blir hackad för att operativsystemet inte har patchats på ett halvår.
5. En sårbarhet finns i själva plattformens *runtime* i Azure App Service.
6. En bugg i leverantörens virtualiseringslager gör att isoleringen mellan två kunder brister.
7. En anställd delar ett känsligt dokument publikt i Google Workspace.
8. Källkod med API-nycklar råkar pushas till ett publikt GitHub-repo.
9. Konton som tillhörde tidigare anställda är fortfarande aktiva och inloggningsbara.
10. Ingen backup är konfigurerad och viktig data går förlorad efter ett användarmisstag.

> **Notering:** Lägg märke till hur ofta svaret landar hos kunden och att det nästan alltid handlar om data, identitet eller konfiguration. Det är precis de tre områden som aldrig försvinner, oavsett tjänstemodell.

## Övning 3 - Räkna på SLA

Ett SLA ser ofta bättre ut än det är. Den här övningen gör siffrorna konkreta och kopplar dem till vad verksamheten faktiskt klarar.

**Efter övningen ska du kunna:**

- räkna om en tillgänglighetsprocent till faktisk nedtid
- resonera kring skillnaden mellan ett SLA och verksamhetens verkliga krav

### Del 1 - Fyll i tabellen

Räkna ut ungefärlig maximal nedtid för varje nivå. Ett år är 8 760 timmar. En månad räknar vi som 30 dagar, alltså 720 timmar.

| SLA | Nedtid per år | Nedtid per månad |
|---:|---:|---:|
| 99 % | ? | ? |
| 99,5 % | ? | ? |
| 99,9 % | ? | ? |
| 99,95 % | ? | ? |
| 99,99 % | ? | ? |

### Del 2 - Koppla till verksamheten

Läs de tre scenarierna och svara på frågorna under varje.

- **Webbshop:** En webbutik har ett SLA på 99,9% för sin plattform. Mest försäljning sker under helgerna.
  - Hur mycket nedtid tillåter det per år?
  - Varför är just den siffran problematisk för den här verksamheten?

- **Journalsystem:** Ett sjukhus får erbjudande om ett system med 99,9% SLA.
  - Räcker det? Motivera.
  - Vad skulle ni ställa för krav i stället?

- **CI/CD-pipeline:** Ett utvecklingsteam använder en byggtjänst med 99,5% SLA.
  - Är nedtiden acceptabel här? Varför, eller varför inte?
  - Skiljer sig kravet från kravet på produktionsmiljön?

### Del 3 - Läs det finstilta

Ett SLA-dokument innehåller formuleringen att tillgänglighetsgarantin bara gäller om kunden har konfigurerat tjänsten med redundans över minst två tillgänglighetszoner.

- Vad betyder det i praktiken om vi kör tjänsten i en enda zon?
- Vem bär ansvaret för nedtiden då?

### Frivillig bonus - räkna med Python

För den som vill kan vi låta koden göra räknandet. Spara filen som `sla_kalkyl.py` och kör den i valfri Python 3.12-miljö.

```python
def nedtid(sla_procent, period_timmar=8760):
    otillganglighet = (100 - sla_procent) / 100
    return otillganglighet * period_timmar

for sla in [99, 99.5, 99.9, 99.95, 99.99, 99.999]:
    timmar = nedtid(sla)
    minuter = timmar * 60
    print(f"{sla:>7} %  ->  {timmar:8.2f} timmar/ar  ({minuter:9.1f} min)")
```

`nedtid(sla_procent, period_timmar=8760)`
: funktionen tar en tillgänglighetsprocent och en period i timmar. Standardvärdet 8760 är antalet timmar på ett år.

`otillganglighet = (100 - sla_procent) / 100`
: räknar ut andelen tid tjänsten får vara nere, som ett decimaltal.

`return otillganglighet * period_timmar`
: multiplicerar andelen med perioden och ger tillbaka nedtiden i timmar.

`for`-slingan
: går igenom en lista med SLA-nivåer och skriver ut nedtiden i både timmar och minuter för varje nivå.

> **Tips:** Vill du köra övningen i en enhetlig miljö oavsett om du sitter på Mac, Windows eller Linux kan du köra skriptet i en container med `docker run --rm -v $(pwd):/app -w /app python:3.12 python sla_kalkyl.py`. Då slipper du  problem med olika Python-versioner. Ladda ned Docker från [docker.com](https://www.docker.com/products/docker-desktop/).

## Övning 4 - Felkonfigurationen

Nu använder vi ansvarsmodellen på riktiga situationer. Felkonfiguration är en av de vanligaste orsakerna till säkerhetsincidenter i molnet och nästan alltid är det kunden som konfigurerat fel, inte leverantören som brustit.

**Efter övningen ska du kunna:**

- identifiera vad som gått fel i en molnmiljö
- placera ansvaret rätt enligt *Shared Responsibility Model*
- föreslå både åtgärd och hur felet kunde upptäckts i tid

### Uppgift

Ta er an minst två av fallen nedan. För varje fall svarar ni på:

1. Vad är det som gått fel?
2. Vems ansvar är det enligt ansvarsmodellen?
3. Hur åtgärdar vi det?
4. Hur skulle vi kunna upptäcka det här i tid? Tänk loggning, larm och uppföljning.

**Fall A - Den öppna lagringen**
En lagringsyta i molnet innehåller kundavtal och personuppgifter. Någon har satt den som publik för att snabbt kunna dela en fil, och sedan glömt att ändra tillbaka. Ytan har varit öppen i flera månader.

**Fall B - Den öppna dörren**
En virtuell server har SSH öppet mot hela internet, alltså 0.0.0.0/0. Kontot `admin` finns kvar med ett svagt lösenord. Ingen MFA, ingen begränsning av vilka IP-adresser som får ansluta.

**Fall C - Spökkontona**
I organisationens SaaS-miljö för e-post och dokument finns tolv konton kvar som tillhörde personer som slutat för över ett år sedan. Flera av dem har fortfarande behörighet till känsliga mappar. MFA är inte påtvingat.

**Fall D - Nyckeln i koden**
En utvecklare har lagt in en API-nyckel direkt i källkoden för att "det var enklast så" och sedan pushat koden till ett publikt repo. Nyckeln ger åtkomst till organisationens lagring i molnet.

## Övning 5 - Publikt, privat eller hybrid?

Den sista övningen tränar leveransmodellerna och tvingar fram ett resonemang.

**Efter övningen ska du kunna:**

- klassificera en miljö som publik, privat eller hybrid
- motivera klassificeringen
- peka ut minst en säkerhetsfråga som modellen väcker

### Del 1 - Klassificera

För varje organisation nedan: är det publikt, privat eller hybrid? Motivera, och peka ut minst en säkerhetsfråga som just den modellen leder till.

- **En redovisningsbyrå** använder Microsoft 365 för e-post och Dropbox för filer. Byrån har ingen egen server, men har ett bokföringsprogram installerat lokalt på kontorets datorer.
- **Ett sjukhus** kör sitt journalsystem i en egen driftad OpenShift-miljö, men skickar avidentifierad statistik till Azure för analys.
- **En myndighet** driver all sin infrastruktur i en egen serverhall. Utvecklarna startar själva resurser via en portal, med automation, skalning och mätning per avdelning.
- **Ett spelbolag** kör i princip allt i AWS och äger ingen egen infrastruktur alls.
- **En bank** kör sina kärnsystem lokalt, men använder Entra ID kopplat till en rad SaaS-tjänster för samarbete och e-post.

### Del 2 - Föreslå en modell

Er grupp ska föreslå en lämplig modell för det här fallet och motivera valet:

> En mindre vårdgivare hanterar patientuppgifter som lyder under sträng lagstiftning, men vill också kunna använda moderna samarbetsverktyg och köra tunga analyser då och då utan att investera i egen hårdvara.

Vilka arbetsbelastningar bör ligga var och varför? Vilka gränser och flöden mellan miljöerna behöver ni hålla extra koll på?

> **Tips:** Nästan alla organisationer blir hybrida i praktiken så fort de börjar använda en SaaS-tjänst parallellt med något lokalt. Fundera på om organisationen ens vet om att den är hybrid och vad det betyder för säkerhetsarbetet.
