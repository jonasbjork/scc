# Cloud Network Security - sammanfattning

> Detta dokument ar AI-genererat och kan innehalla fel.

Det har ar en sammanfattning av kapitlet Cloud Network Security. Den ar tankt att fungera som ett stod infor lektionen och som repetition efterat, inte som en ersattning for kapitlet i sin helhet.

> Endast det som maste vara publikt ska vara publikt, och enbart nodvandig trafik ska tillatas.

Den har meningen ar karnan i hela kapitlet. Allt vi gar igenom nedan, segmentering, security groups, bastion hosts, VPN, ar bara olika konkreta satt att forverkliga precis den principen.

## Varfor natverkssakerhet i molnet ar en egen sak

I ett traditionellt natverk fanns en tydlig grans. Innanfor brandvaggen, pa det interna natet, litade vi pa trafiken. Utanfor var det farligt. Modellen fungerade nagorlunda sa lange alla servrar stod i eget serverrum och alla anstallda satt pa kontoret.

I molnet finns ingen sjalvklar fysisk grans. Resurser skapas och tas bort dynamiskt, tjanster kommunicerar over natverk vi inte ager, och anstallda ansluter fran hemmet, kaféer och andra lander. Vi kan inte rita en cirkel runt infrastrukturen och saga att allt innanfor ar betrott. Istallet maste varje resurs, varje trafikflode och varje anslutning vara ett aktivt beslut, inte ett antagande.

## Natverk i molnet jamfort med traditionella natverk

Det traditionella natverket byggde pa en fysisk brandvagg, *VLAN* for att dela upp interna system, och en *DMZ* (demilitariserad zon) for tjanster som skulle vara natbara utifran men anda halls separerade fran det interna natet. Svagheten var att tilliten till det interna natet ofta var for hog: en angripare som tog sig innanfor brandvaggen kunde ofta rora sig fritt.

Molnnatverket ar mjukvarudefinierat istallet for fysiskt. Grundbyggstenen ar *VPC* (AWS) eller *VNet* (Azure), det virtuella natverksutrymme vi sjalva kontrollerar. Managed services, som databaser och kotjanster, sitter ofta inte ens i vart natverk utan nas via egna endpoints, dar identitet och natverk vavs ihop pa ett satt som saknar motsvarighet i traditionella miljoer. Den stora skillnaden ar ocksa hastigheten: en resurs kan skapas pa sekunder, med de regler vi anger.

Det har hastigheten ar ett dubbelriktat svard. En enda rad kan exponera en tjanst mot hela internet:

```
0.0.0.0/0 TCP 22 ALLOW
```

`0.0.0.0/0` betyder alla IP-adresser pa hela internet. `TCP 22` ar porten for SSH, administratorsatkomst till servern. Regeln betyder i praktiken att hela internet far ansluta till SSH. Det ar inte hypotetiskt, automatiserade skanningsverktyg hittar exponerade portar inom minuter och borjar direkt med inloggningsforsok.

> I det traditionella natverket skyddade arkitekturen oss delvis mot egna misstag, det kravdes flera aktiva steg for att exponera nagot. I molnet finns inget som skyddar oss automatiskt. Varje resurs ar sa exponerad som vi konfigurerar den att vara.

## VPC och VNet

*VPC* och *VNet* ar foretagets virtuella natverksomrade i molnet, den grans vi ritar runt vara resurser. Inuti den gransen delar vi upp natverket i *subnat*, mindre sakerhetszoner.

Ett *publikt subnat* har en vag till internet, det ar dar vi placerar sadant som faktiskt ska vara natbart, till exempel en lastbalanserare. Ett *privat subnat* saknar direkt vag in eller ut, det ar dar databaser och interna tjanster ska bo. Behover en resurs i ett privat subnat na internet, for att hamta uppdateringar till exempel, sker det via en *NAT gateway*, en kontrollerad envägsdörr for utgaende trafik. Det ar *routingtabellen* som i praktiken avgor om ett subnat ar publikt eller privat.

*Security groups* fungerar som virtuella brandvaggar kopplade direkt till en enskild resurs, inte till ett helt segment. De ar *stateful*, vilket betyder att om vi tillater en inkommande anslutning slapps svaret automatiskt igenom, utan att vi behover oppna for returtrafiken explicit.

*Network ACL* (*NACL*) ar ett kompletterande lager som verkar pa subnatsniva istallet for resursniva. Till skillnad fran security groups ar NACL *stateless*, vilket betyder att vi maste tillata trafik i bada riktningarna sjalva. I praktiken anvands security groups for det detaljerade arbetet och NACL som ett grovt komplement.

En *publik IP-adress* ar synlig fran hela internet, security groups avgor sedan vad som far kommas at. En *privat IP-adress* nas bara inifran natverket eller via en kontrollerad vag som VPN. Franvaron av en publik adress ar ett starkare skydd an vilken brandvaggsregel som helst, utan adress finns det helt enkelt ingenting att ansluta till.

Nar en resurs i ett privat subnat behover administreras anvander vi en *bastion host* (*jump server*), en hart hardad server i ett publikt subnat som fungerar som den enda vagen in. Den ska ha MFA, vara begransad till kanda administrators-IP-adresser, logga all aktivitet till en extern plattform, och kora sa lite mjukvara som mojligt.

> En bastion som ar felkonfigurerad, till exempel oppen mot hela internet utan MFA eller loggning, ar varre an ingen bastion alls. Den ger en falsk kansla av trygghet medan den i praktiken ar en valkand exponerad vag in.

## Publika och privata resurser

En vanlig trelagersapplikation bestar av ett presentationslager, ett applikationslager och ett datalager. Varje lager ska bara kommunicera med lagret direkt over eller under, och sa fa lager som mojligt ska vara synliga fran internet.

| Komponent | Publik? | Motivering |
|---|---|---|
| Frontend / lastbalanserare | Ja, begransat | Endast HTTPS 443, det ar hela syftet med komponenten |
| Backend / applikationsserver | Nej | Anvandaren behover aldrig ansluta direkt |
| Databas | Nej | Storst skada vid intrang, ska bara nas fran backend |
| Backup | Nej | Innehaller per definition en kopia av all kanslig data |
| Monitoring och loggning | Nej eller begransat | Samlar kanslig systeminformation |
| Adminatkomst | Nej, inte direkt | Hanteras via bastion, VPN eller identitetsstyrd atkomst |

Databasen forjanar sarskild uppmarksamhet. Automatiserade skanningsverktyg soker kontinuerligt av internet efter oppna databasportar, PostgreSQL pa 5432, MySQL pa 3306, MongoDB pa 27017. En exponerad databas hittas ofta inom minuter och borjar attackeras inom timmar.

Det gemensamma temat ar minsta mojliga exponering, samma princip som *least privilege* inom IAM, nu tillampad pa natverksniva. Det som inte ar exponerat kan inte attackeras utifran.

## Security groups och brandvaggsregler

En security group-regel bestar av fyra delar: riktning (in eller ut), protokoll (TCP, UDP eller annat), port eller portintervall, samt kalla eller destination. Grundprincipen ar att borja med att neka allt och sedan oppna specifikt for det vi faktiskt behover.

| Regel | Bedomning |
|---|---|
| Internet -> Frontend TCP 443 | OK |
| Backend -> Databas TCP 5432 | OK |
| Admin-IP -> Bastion TCP 22 | OK, begransad kalla |
| Internet -> Backend TCP 8080 | Onodig och farlig |
| Internet -> Databas TCP 5432 | Farlig |
| Internet -> SSH TCP 22 fran 0.0.0.0/0 | Mycket farlig |

Kallan eller destinationen i en regel kan vara en annan security group istallet for ett IP-intervall. Vi kan skriva "resurser med security group backend-sg far ansluta till databasen" istallet for att peka ut en enskild IP-adress. Om backend-servern byts ut eller skalas till fler instanser behover vi inte uppdatera databasens regler, de galler automatiskt for allt som tillhor rätt security group.

For oppna regler ar ett av de vanligaste fynden i molnsakerhetsgranskningar, sallan av illvilja utan for att en port oppnades for ett akut problem och aldrig stangdes igen. En bra rutin ar att regelbundet fraga sig varfor en regel finns, om ingen kan svara ar det ett tecken pa att den bor granskas.

## Network ACLs

NACL sitter vid ingangen till hela subnatet och paverkar alla resurser dar pa en gang, medan security groups sitter vid varje enskild resurs. NACL passar bra for grova regler, till exempel att blockera ett kant skadligt IP-intervall en gang for hela subnatet. Eftersom NACL ar stateless och kraver regler i bada riktningarna blir det latt svaroverskadligt om vi forsoker anvanda det for detaljerade regler, darfor sköter security groups det detaljerade arbetet i praktiken.

## Segmentering

Segmentering delar upp natverket i sakerhetszoner med kontrollerade kommunikationsvagar mellan dem. Principen bygger pa *assume breach*: vi designar natverket som om ett intrang kommer att ske, och fragar oss hur vi begransar skadan nar det gor det.

Ett *platt natverk*, dar alla resurser kan na varandra fritt, ger en angripare som komprometterat en enda resurs en utmarkt startpunkt for att utforska allt annat. En vandesignad miljo delar istallet in natverket i zoner:

- Publik zon: lastbalanserare, webbservrar, API-gateways
- Applikationszon: backend-tjanster, nas fran den publika zonen
- Databaszon: nas bara fran applikationszonen
- Administrationszon: bastion och managementverktyg, strikt begransad
- Backupzon: nas av det som sakerhetskopieras, men har ingen vag tillbaka till applikationerna
- Monitoring- och loggzon: tar emot data fran andra zoner, men har ingen vag tillbaka

Segmentering ersatter inte andra skyddslager. En valsegmenterad miljo med svaga losenord ar fortfarande sarbar, men skadan begransas och vi far tid att upptacka och stoppa intranget innan det sprider sig.

## Mikrosegmentering

Vanlig segmentering kontrollerar trafiken mellan zoner, men en zon kan fortfarande innehalla manga tjanster som nar varandra fritt inom zonen. Mikrosegmentering definierar regler mellan enskilda tjanster istallet for mellan zoner: "bara tjanst A far na databas B", inte "applikationszonen far na databaszonen".

Det ar mest praktiskt genomforbart i miljoer som redan ar mjukvarudefinierade. I *Kubernetes* och *OpenShift* styr *NetworkPolicies* exakt vilka pods som far kommunicera med vilka andra, utan en sadan policy tillater Kubernetes som standard all intern trafik. I en *service mesh* (Istio, Linkerd) hanteras kommunikationen av en proxy vid varje tjanst, som ofta implementerar *mTLS* automatiskt.

Det klassiska hotet mikrosegmentering skyddar mot ar *lateral rorelse*, att en angripare som komprometterat ett system rör sig vidare till andra system i samma miljo. Mikrosegmentering okar samtidigt komplexiteten, darfor introduceras den ofta gradvis, med de kansligaste tjansterna forst.

## Bastion hosts och jump hosts

En bastion host ar den enda, hart kontrollerade vagen in till privata resurser. Istallet for att oppna administrativa portar direkt pa varje server mot internet, koncentreras all administrativ atkomst till en enda dorr som gar att skydda och overvaka ordentligt.

En val konfigurerad bastion har:

- MFA eller stark autentisering
- Atkomst begransad till godkanda IP-adresser, aldrig fran 0.0.0.0/0
- Loggning av all aktivitet, skickad till en plattform bastionen sjalv inte kan andra
- Hardning och minimalt med mjukvara
- Begransade anvandare, gärna med tidsbegransad atkomst

Bastionen ar samtidigt sjalv ett attraktivt mal, eftersom en angripare som komprometterar den potentiellt far tillgang till hela den privata infrastrukturen bakom. Den ska darfor behandlas som en kritisk resurs, patchas regelbundet och granskas kontinuerligt. I moderna molnmiljoer ersatts den klassiska bastionen ibland av identitetsstyrda atkomstlosningar, dar principen ar densamma men implementationen modernare.

## VPN och private endpoints

*VPN* skapar en krypterad tunnel mellan tva punkter. Aven om trafiken fysiskt fardas over det oppna internet ser den ut som brus for den som avlyssnar. Vanliga anvandningsfall ar kontor till moln, administrator till moln, datacenter till moln, och moln till moln.

En *private endpoint* ar en privat natverksanslutning direkt in till en molntjanst, till exempel en databas eller secrets manager, utan att trafiken nagonsin lamnar det privata natverket. Tjansten far en privat IP-adress i vart eget natverk och nas som om den vore en intern resurs.

VPN skyddar trafiken pa vagen till molnmiljon, private endpoints skyddar trafiken inne i molnmiljon. Ingen av mekanismerna ar tillrackliga ensamma, tillsammans med security groups, segmentering och bastion bygger de en skiktad arkitektur dar ett enskilt brustet lager inte ger fri vag.

## Natverkskryptering

*TLS* ligger under nastan all modern natverkskryptering och etablerar en krypterad kanal efter att serverns identitet verifierats med ett certifikat. *HTTPS* ar HTTP over TLS och ska vara standard for all webbtrafik, aven intern trafik som "inte innehaller kanslig information".

*mTLS* (*mutual TLS*) later bade klient och server bevisa sin identitet kryptografiskt, till skillnad fran vanlig TLS dar bara servern verifieras. Det ar standardmekanismen for saker tjanst-till-tjanst-kommunikation i service mesh och Zero Trust-arkitekturer. Aven databasanslutningar ska alltid vara TLS-krypterade, nagot som inte alltid ar pastaget som standard utan maste konfigureras och verifieras aktivt.

Kryptering skyddar inte mot allt:

- Lackta credentials, angriparen ansluter da precis som en legitim klient
- En sarbar backendapplikation, en SQL-injection fungerar lika bra bakom HTTPS
- Felaktiga behorigheter, en krypterad anslutning med for hoga rattigheter
- Data som loggas i klartext trots krypterad transport
- Publik exponering av fel tjanst, kryptering gor intrangsforsoket krypterat, inte databasen mindre exponerad

> En valkrypterad kommunikationskanal till en daligt konfigurerad tjanst ar fortfarande en daligt konfigurerad tjanst. Kryptering och arkitektur maste ga hand i hand.

## Zero Trust Networking

I den traditionella modellen var tilliten kopplad till platsen, en resurs pa det interna natet ansags betrodd. Zero Trust ersatter platsbaserad tillit med kontinuerlig verifiering. Varje atkomstforsok stalls infor samma fragor:

- Vem ar du? (identitet, inte plats)
- Vilken tjanst forsoker du na?
- Behover du verkligen na den? (least privilege)
- Fran vilken plats och enhet?
- Loggas atkomsten?

I natverksarkitekturen syns Zero Trust genom mikrosegmentering med explicita regler per tjanst, mTLS mellan tjanster, private endpoints, identitetsstyrd administrativ atkomst, och kontinuerlig utvardering av sessioner snarare an bara vid inloggningstillfallet. Ingen av mekanismerna ar Zero Trust i sig sjalv, de ar verktyg som tillsammans forverkligar principerna.

## Loggning av natverkstrafik

Utan loggning vet vi ingenting om vad som faktiskt sker i natverket. Vi loggar for att kunna felsoka, utreda incidenter, upptacka scanning tidigt, se ovanliga trafikmonster som kan avslöja lateral rorelse, och for sparbarhet och regelefterlevnad (GDPR, PCI DSS).

Vanliga loggtyper ar *flow logs* (kalla, destination, port, protokoll, tillaten eller nekad), firewall-loggar, load balancer-loggar, VPN-loggar, DNS-loggar (kan avslöja *DNS tunneling*), proxy-loggar, WAF-loggar och authentication-loggar.

Loggar ar bara vardefulla om de faktiskt anvands, darfor skickas de till en central plattform, ett *SIEM* (*Security Information and Event Management*), dar de kan analyseras och korreleras. Loggplattformen ska sjalv ligga i en separat zon med strikt atkomstkontroll, och loggarna ska inte kunna modifieras av de system som genererar dem. En angripare som kan radera sina egna spar har gjort utredningen mycket svarare.

## Sammanfattning

- Molnet saknar en fysisk grans, varje resurs, varje trafikflode och varje anslutning maste vara ett aktivt beslut.
- VPC/VNet, subnat, security groups och NACL ar byggstenarna vi anvander for att styra vad som ar publikt och vad som far kommunicera med vad.
- Trelagersarkitekturen visar att nastan ingenting behover vara publikt, oftast bara en lastbalanserare pa port 443.
- Segmentering och mikrosegmentering begransar skadan om ett intrang trots allt sker, genom att stoppa lateral rorelse.
- Bastion, VPN och private endpoints ger kontrollerade vagar in, istallet for att exponera administrativa portar eller managed services direkt mot internet.
- Kryptering skyddar trafiken under transport, men ersatter aldrig ratt arkitektur, ratt behorigheter eller ratt konfiguration.
- Zero Trust ersatter platsbaserad tillit med kontinuerlig verifiering, i natverket lika mycket som i identitetshantering.
- Loggning gor natverket observerbart, och loggarna maste sjalva skyddas for att vara vardefulla vid en incident.

> Endast det som maste vara publikt ska vara publikt, och enbart nodvandig trafik ska tillatas.
