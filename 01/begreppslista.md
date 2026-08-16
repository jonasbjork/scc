> Detta dokument är AI-genererat och kan innehålla fel

# Secure Cloud Computing - begreppslista

Det här är en lista över de viktigaste begreppen i kapitlet om säker molndrift. Tanken är att vi ska kunna slå upp ett ord snabbt utan att leta igenom hela materialet. Begreppen står i bokstavsordning, och de engelska termerna står med sin svenska förklaring bredvid.

**Authorization (auktorisering)**
Kontroll av vad en redan autentiserad användare eller tjänst faktiskt får göra. I molnbaserade tjänster är det en central del av åtkomstkontrollen, skild från själva inloggningen.

**AWS (Amazon Web Services)**
En av de tre dominerande publika molnleverantörerna. Beskriver ansvarsfördelningen som *security of the cloud* (leverantörens ansvar för infrastrukturen) och *security in the cloud* (kundens ansvar för det som körs inuti).

**Broad network access (nätverksåtkomst)**
En av NIST:s fem egenskaper. Tjänsterna nås via standardiserade nätverksprotokoll oavsett om vi använder en bärbar dator, en mobil eller en server.

**CI/CD**
*Continuous Integration* och *Continuous Delivery*. Automatiserade flöden för att bygga, testa och driftsätta kod. GitHub Actions kan delvis ses som en plattformstjänst för CI/CD-pipelines.

**Cloud computing (molntjänster)**
En modell där IT-resurser levereras som tjänster över ett nätverk istället för att ägas och driftas lokalt. Handlar inte bara om *var* datorerna står, utan om hur resurser levereras, skalas, mäts och hur ansvaret fördelas.

**Colocation (co-location)**
Vi hyr plats för vår egen hårdvara i någon annans datacenter, som sköter ström, kyla och fysisk säkerhet. Uppfyller inte NIST:s kriterier och är alltså inte molndrift.

**Data LifeCycle**
Datans livscykel, från att den skapas till att den arkiveras eller raderas. En viktig utgångspunkt när vi ska skydda data i molnbaserade miljöer.

**Datakryptering**
Att göra data oläsbar för den som inte har rätt nyckel. Skyddar informationen när den lagras och när den lämnar organisationen.

**Disaster Recovery**
Planering och åtgärder för att återställa system och data efter ett allvarligt avbrott. Kopplas alltid till RTO och RPO.

**GDPR**
EU:s dataskyddsförordning. Även när vi använder en SaaS-tjänst är det organisationen, inte leverantören, som är personuppgiftsansvarig.

**Hybrid cloud (hybridmoln)**
En kombination av privat och publik infrastruktur. Den modell de flesta organisationer faktiskt använder, ofta utan att kalla det så.

**Hypervisor**
Programvaran som skapar och kör de virtuella maskinerna på en fysisk server.

**IaaS (Infrastructure as a Service)**
Vi hyr virtuell infrastruktur - server, nätverk och lagring - av en molnleverantör. Vi ansvarar själva för operativsystem, applikationer, konfiguration och all data.

**IAM (Identity and Access Management)**
Hantering av identiteter, roller och behörigheter. I molnet styrs mycket av åtkomsten via IAM-policys, och identitet är ofta den nya gränsen.

**Measured service (mätbara tjänster)**
En av NIST:s fem egenskaper. Resursanvändningen mäts, och vi betalar för det vi faktiskt förbrukar.

**MFA (Multi-Factor Authentication)**
Inloggning som kräver mer än bara ett lösenord. Skyddar kontot även om lösenordet läcker.

**Middleware**
Ett programvarulager mellan operativsystemet och applikationen. Hanteras av leverantören i en PaaS-tjänst.

**NIST (National Institute of Standards and Technology)**
Amerikansk myndighet vars definition av molntjänster används som referens i branschen. Definitionen finns i dokumentet *SP 800-145*.

**On-demand self-service**
En av NIST:s fem egenskaper. Vi kan starta upp resurser på egen hand, utan att kontakta support eller vänta på manuell konfiguration.

**On-prem (on-premises)**
Drift i egen regi, oftast i en egen serverhall, där organisationen ansvarar för i princip allt.

**PaaS (Platform as a Service)**
Vi fokuserar på kod, data och identiteter medan leverantören hanterar operativsystem, *runtime* och skalning. Ansvaret för applikationssäkerheten ligger fortfarande hos oss.

**Private cloud (privat molntjänst)**
En molnmiljö dedikerad till en enda organisation, där resurser inte delas med andra kunder. Kan drivas internt eller av en extern leverantör.

**Public cloud (publik molntjänst)**
En molnmiljö där många kunder delar samma underliggande infrastruktur som leverantören äger och driver. AWS, Azure och Google Cloud är de dominerande.

**Rapid elasticity (snabb skalbarhet)**
En av NIST:s fem egenskaper. Vi kan skala upp och ner resurser snabbt, ibland automatiskt, beroende på behovet.

**Resource pooling (resursdelning)**
En av NIST:s fem egenskaper. Leverantören delar sina fysiska resurser mellan många kunder, utan att kunderna ser varandras data.

**Retention policy**
Regler för hur länge data sparas. Saknas den kan data försvinna utan möjlighet att återskapa den.

**RPO (Recovery Point Objective)**
Hur mycket data vi har råd att förlora vid ett avbrott, uttryckt som en tidsrymd. Styr bland annat hur ofta vi behöver ta backup.

**RTO (Recovery Time Objective)**
Hur länge en verksamhet klarar att ha ett system nere innan det blir ett verkligt problem.

**Runtime (runtime environment)**
Miljön som kör applikationen. Hanteras av leverantören i en PaaS-tjänst.

**SaaS (Software as a Service)**
Vi använder en färdig applikation som leverantören driver och underhåller. Vi installerar och patchar ingenting, men ansvarar fortfarande för användare, åtkomst, data och efterlevnad.

**Secrets**
Känsliga uppgifter som lösenord, API-nycklar och tokens. Ska aldrig exponeras i kod.

**Security groups**
Leverantörens benämning på brandväggsregler för virtuella resurser, framför allt i IaaS.

**Service Level Agreement (SLA)**
Leverantörens löfte om vilken tjänstenivå vi kan förvänta oss, framför allt tillgänglighet. Ett juridiskt dokument, inte en säkerhetsfunktion, och inte en garanti för att verksamheten klarar ett avbrott.

**Shadow IT**
Tjänster och system som används i organisationen utan att vara sanktionerade, upphandlade eller granskade ur ett säkerhetsperspektiv. Börjar oftast med SaaS.

**Shared Responsibility Model (modellen för delat ansvar)**
Beskriver vem som ansvarar för vad i en molntjänst. Gränsen varierar beroende på om vi använder IaaS, PaaS eller SaaS, men data, identiteter och efterlevnad är alltid kundens ansvar.

**Tillgänglighet (availability)**
Hur stor andel av tiden en tjänst är i drift, mätt i procent. Ju fler nior i siffran, desto mindre nedtid tillåts per år.

**Vendor lock-in (leverantörsberoende)**
Att vi låser fast oss hos en leverantör och får svårt att byta. Det är ofta svårare att flytta data *ut* ur en molntjänst än det var att lägga in den.

**Virtualisering**
Att köra flera virtuella servrar på samma fysiska maskin. Förändrar hur hårdvaran används, men inte i sig vem som ansvarar för den.

**VPC (Virtual Private Cloud)**
Ett logiskt isolerat, privat nätverk inuti en publik molnmiljö.
