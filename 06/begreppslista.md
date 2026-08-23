# Cloud Network Security - begreppslista

> Detta dokument ar AI-genererat och kan innehalla fel.

Har foljer de viktigaste begreppen fran kapitlet Cloud Network Security, i alfabetisk ordning. Anvand listan som uppslagsverk vid repetition eller nar ett ord dyker upp igen langre fram i kursen.

Assume breach
: Ett tankesatt dar vi designar system och natverk som om ett intrang kommer att ske. Fragan blir da inte om ett intrang inträffar, utan hur vi begransar skadan nar det gor det. Grunden for bade segmentering och Zero Trust.

Authentication-logg
: Logg over inloggningsforsok, bade lyckade och misslyckade, mot tjanster och system. Kombinerad med natverksloggar visar den bade vem som forsokt ansluta och varifran.

Bastion host (jump host, jump server)
: En hart hardad server i ett publikt subnat som fungerar som den enda tillatna vagen in till resurser i privata subnat. Ska ha MFA, vara begransad till kanda administrators-IP-adresser, och logga all aktivitet externt.

CIDR-notation
: Sattet vi skriver IP-adressintervall pa, till exempel `0.0.0.0/0` (alla adresser pa internet) eller `210.211.212.213/32` (exakt en adress). Siffran efter snedstrecket anger hur manga adresser regeln galler for.

DMZ (demilitariserad zon)
: I traditionella natverk en zon for tjanster som ska vara natbara utifran, men som anda halls separerad fran det interna natverket. Motsvarigheten till ett publikt subnat i molnet.

DNS-logg
: Logg over domanuppslag. Kan avslöja kommunikation med kanda skadliga domaner samt *DNS tunneling*.

DNS tunneling
: En teknik dar en angripare anvander DNS-protokollet for att smuggla ut data fran en komprometterad miljo, ofta i syfte att undvika andra kontroller.

Firewall-logg
: Logg over vad en brandvagg eller security group har tillatit och nekat. Vardefull for att se vad som forsokt na en resurs, aven blockerad trafik.

Flow log
: Logg over natverksflöden: kalla, destination, port, protokoll, mangd data, samt om trafiken tillats eller nekades. Innehaller inte det faktiska innehallet i trafiken.

HTTPS
: HTTP over TLS, den krypterade varianten av webbkommunikation. Ska vara standard for all webbtrafik, aven intern trafik.

IAM (Identity and Access Management)
: Hanteringen av identiteter och atkomstrattigheter. Natverkssakerhet och IAM vavs allt mer ihop i molnet, till exempel nar en security group refererar till en annan istallet for till en IP-adress.

Kubernetes NetworkPolicy
: En regel i Kubernetes som styr exakt vilka pods som far kommunicera med vilka andra, pa vilka portar och med vilket protokoll. Utan en NetworkPolicy tillater Kubernetes som standard all intern trafik mellan pods.

Lateral rorelse
: Nar en angripare som redan komprometterat ett system rör sig vidare till andra system i samma miljo. Det klassiska hotet som segmentering och mikrosegmentering ar designade for att stoppa.

Least privilege
: Principen att en identitet eller resurs bara ska ha exakt den atkomst som behovs, inte mer. Tillampad pa natverksniva blir principen minsta mojliga exponering.

Load balancer-logg
: Logg over inkommande forfragningar till en lastbalanserare: kall-IP, vilket backend-system som hanterade forfragan, svarstid och HTTP-statuskod.

Managed service
: En molntjanst, till exempel en databas eller en ko, som molnleverantoren driftar at oss. Ofta placerad utanfor vart eget virtuella natverk och nadd via en egen endpoint, dar atkomst styrs av en kombination av natverksregler och IAM-policyer.

Mikrosegmentering
: Natverkskontroll pa tjanstniva istallet for zonniva. Varje kommunikationsvag mellan tva enskilda tjanster ar ett explicit beslut, oavsett vilken sakerhetszon tjansterna befinner sig i.

mTLS (mutual TLS)
: TLS med omsesidig autentisering. Bade klient och server bevisar sin identitet kryptografiskt, till skillnad fran vanlig TLS dar bara servern verifieras. Vanligt i service mesh och Zero Trust-arkitekturer.

NACL (Network ACL)
: Ett kompletterande sakerhetslager som verkar pa subnatsniva istallet for resursniva. Till skillnad fran security groups ar NACL stateless.

NAT gateway
: En kontrollerad utgangspunkt som later resurser i ett privat subnat na internet for utgaende trafik, till exempel for att hamta uppdateringar, utan att oppna for inkommande trafik.

Platt natverk
: Ett natverk utan segmentering, dar alla resurser i princip kan na varandra fritt. En komprometterad resurs ger da en angripare en utmarkt startpunkt for att rora sig vidare.

Private endpoint
: En privat natverksanslutning direkt in till en molntjanst, dar trafiken aldrig lamnar det privata natverket. Tjansten far en privat IP-adress i vart eget natverk och nas som en intern resurs.

Privat IP-adress
: En IP-adress som bara ar natbar inifran det virtuella natverket, eller via en kontrollerad vag som VPN eller private endpoint. Kan inte anropas direkt fran internet.

Privat subnat
: Ett subnat utan direkt vag till eller fran internet. Dar placerar vi databaser, interna applikationsservrar och andra kansliga tjanster.

Proxy-logg
: Logg over utgaende webbtrafik via en proxyserver. Ger synlighet i vilka externa resurser interna system kommunicerar med, och kan avsloja kommunikation med *command and control*-servrar.

Publik IP-adress
: En IP-adress som ar tillganglig fran hela internet. Security groups och NACL bestammer vad som far kommas at pa adressen, men adressen i sig gar att hitta.

Publikt subnat
: Ett subnat med en route till internet, dar vi placerar resurser som faktiskt ska vara natbara utifran, till exempel en lastbalanserare.

Routingtabell
: En uppsattning regler kopplad till ett subnat som styr vart trafik skickas. Det ar routingtabellen som i praktiken avgor om ett subnat ar publikt eller privat.

Sakerhetszon
: En avgransad del av natverket med ett tydligt syfte och en egen sakerhetsniva, till exempel den publika zonen, applikationszonen, databaszonen eller administrationszonen. Kommunikation mellan zoner ska vara en aktiv, motiverad regel.

Security group
: En virtuell brandvagg kopplad direkt till en enskild resurs, till exempel en server eller en databas. Stateful, vilket betyder att returtrafik pa en tillaten inkommande anslutning slapps igenom automatiskt.

Segmentering
: Att dela upp natverket i separata sakerhetszoner med kontrollerade kommunikationsvagar mellan dem, sa att ett komprometterat system inte automatiskt kan na allt annat.

Service mesh
: En infrastrukturkomponent, till exempel Istio eller Linkerd, som hanterar kommunikation mellan tjanster via en proxy bredvid varje tjanst. Mojliggor kryptering, styrning och loggning av all intern trafik pa tjanstniva.

SIEM (Security Information and Event Management)
: En central plattform dit loggar fran olika kallor skickas for att analyseras, korreleras och generera larm vid avvikelser.

Stateful
: En egenskap hos security groups som innebar att om vi tillater en inkommande anslutning tillats aven svarstrafiken automatiskt, utan att vi behover skriva en separat regel for den.

Stateless
: En egenskap hos NACL som innebar att trafik maste tillatas explicit i bada riktningarna, in och ut, for att en anslutning ska fungera.

Trelagersarkitektur
: En vanlig applikationsstruktur med ett presentationslager, ett applikationslager och ett datalager, dar varje lager bara ska kommunicera med lagret direkt over eller under.

TLS (Transport Layer Security)
: Protokollet som ligger under nastan all modern natverkskryptering. Etablerar en krypterad kanal mellan tva parter efter att serverns identitet verifierats med ett certifikat.

VLAN
: En teknik for att dela upp ett fysiskt natverk i logiska segment i traditionella, lokala natverksmiljoer. Foregangaren till subnat i molnet.

VPC (Virtual Private Cloud) / VNet
: Det virtuella natverksutrymme vi kontrollerar i molnet. AWS kallar det VPC, Azure kallar det VNet. Grunden som subnat, security groups och ovriga byggstenar placeras inuti.

VPN (Virtual Private Network)
: En krypterad tunnel mellan tva punkter, till exempel mellan ett kontor och en molnmiljo, eller mellan en administrators dator och en molnmiljo. Skyddar trafiken pa vagen, aven nar den fysiskt passerar det oppna internet.

WAF (Web Application Firewall)
: En brandvagg specialiserad pa trafik mot webbapplikationer. Filtrerar bort bland annat SQL-injection-forsok och XSS-attacker, och loggar det som stoppas.

Zero Trust
: Ett sakerhetstankesatt dar vi aldrig litar pa nagot automatiskt bara for att det befinner sig pa det interna natverket. Varje atkomstforsok verifieras kontinuerligt utifran identitet, resurs, kontext och behov, oavsett varifran forfragan kommer.
