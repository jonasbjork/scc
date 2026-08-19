> Detta dokument är AI-genererat och kan innehålla fel

# Begreppslista: Hemligheter, nycklar och kryptering

Det här är en alfabetisk lista över de begrepp vi går igenom i kapitlet om hemligheter, nycklar och kryptering. Tanken är att vi snabbt ska kunna slå upp en term och få en kort, tydlig förklaring. För en mer utförlig genomgång hänvisar vi till kapitlet och sammanfattningen.

---

**API-nyckel**
: En secret som identifierar och autentiserar den som anropar ett API. Läcker den ut ger den direkt åtkomst, oavsett att datan bakom API:et är krypterad.

**Argon2**
: En modern, medvetet långsam hashalgoritm designad för lösenordslagring. Väljs framför snabba algoritmer som SHA-256 just för att den gör det svårt att gissa många lösenord i sekunden.

**Asymmetrisk kryptering**
: Kryptering med ett *nyckelpar* i stället för en delad nyckel. Det som krypteras med den publika nyckeln kan bara dekrypteras med den privata. Löser problemet med säker nyckeldelning, men är långsam och kombineras därför ofta med symmetrisk kryptering.

**Autenticitet**
: Garantin att innehållet verkligen skapades eller godkändes av den påstådda avsändaren. En av de två egenskaper en digital signatur ger oss.

**bcrypt**
: En långsam hashalgoritm för lösenordslagring, i samma familj som scrypt och Argon2.

**BYOK (Bring Your Own Key)**
: En modell där vi själva äger och kontrollerar krypteringsnycklarna, även när datan lagras hos en molnleverantör. Ger mer kontroll men också fullt ansvar - förlorar vi nyckeln är datan permanent oåtkomlig.

**CA (Certificate Authority)**
: En betrodd utfärdare som signerar certifikat och därmed går i god för att en publik nyckel hör ihop med en viss identitet.

**Certifikat**
: En signerad försäkran om att en viss publik nyckel tillhör en viss identitet. Innehåller bland annat namn, publik nyckel, utfärdare, giltighetstid och CA:ns signatur.

**Certifikatkedja**
: Den hierarkiska tilliten från *servercertifikat* via *mellanliggande CA* upp till en *rot-CA* som webbläsaren känner igen. Saknas en länk kan kedjan inte byggas och certifikatet avvisas.

**Confidential computing (konfidentiell databehandling)**
: Ett teknikområde för att skydda *data in use* genom att köra kod i isolerade, hårdvaruskyddade miljöer (*TEE*) där inte ens operativsystemet eller molnleverantören kan läsa innehållet.

**CRL (Certificate Revocation List)**
: En lista över certifikat som återkallats innan de gått ut. Ett sätt för klienter att kontrollera om ett certifikat fortfarande är giltigt.

**Data at rest**
: Data som ligger lagrad och väntar, till exempel i databaser, backuper, loggar och på diskar. Skyddas med kryptering, ofta i flera nivåer samtidigt.

**Data in transit**
: Data på väg via ett nätverk. Skyddas med TLS, mTLS, SSH, VPN och liknande. Det grundläggande hotet är avlyssning.

**Data in use**
: Data som aktivt behandlas i minne eller av processorn. Är per definition dekrypterad och kan därför inte skyddas med kryptering på samma sätt - i stället använder vi isolering och least privilege.

**Databaskryptering**
: Kryptering av data i en databas, antingen av hela lagringsfilen på disknivå eller av enskilda fält och kolumner (*fältkryptering*), till exempel just kolumnen med personnummer.

**Dekryptering**
: Att göra krypterad data läsbar igen med rätt nyckel. Motsatsen till kryptering.

**Digest**
: Ett annat ord för *hashvärde* - resultatet av en hashfunktion.

**Digital signatur**
: Se *Signering*.

**Diskkryptering**
: Kryptering av en hel disk eller lagringsvolym. Skyddar mot fysiska hot som en stulen eller felaktigt kasserad disk.

**Envägsfunktion**
: En funktion som bara går att beräkna åt ett håll. En hashfunktion är en envägsfunktion - vi kan inte räkna oss tillbaka till ursprungsdatan från hashvärdet.

**HSM (Hardware Security Module)**
: Dedikerad hårdvara byggd för att lagra och använda kryptografiska nycklar. Nyckeln lämnar aldrig hårdvaran i klartext, och enheten nollställer sig vid fysiskt intrång. Används där kraven är ovanligt höga, som hos banker och certifikatutfärdare.

**HashiCorp Vault**
: En plattformsoberoende tjänst för att hantera nycklar och hemligheter, oberoende av molnleverantör. Den öppna varianten heter *OpenBao*.

**Hashfunktion**
: En funktion som tar indata av godtycklig storlek och producerar ett värde av fast längd - ett *hashvärde*. Går bara åt ett håll och har ingen nyckel.

**Hashning**
: Processen att beräkna ett hashvärde. Används för filintegritet, lösenordslagring och som en del av digitala signaturer. Döljer inte innehåll - den bevisar bara att något inte ändrats.

**Hashvärde**
: Resultatet av en hashfunktion. En enda ändrad bit i indatan ger ett helt annat hashvärde.

**HTTP**
: Protokollet för webbtrafik utan kryptering. All trafik går i klartext och kan läsas av den som avlyssnar nätverket. Ska inte användas i moderna system.

**HTTPS**
: HTTP skyddat av TLS. Trafiken är krypterad och serverns identitet verifierad med ett certifikat.

**IAM (Identity and Access Management)**
: Hantering av identiteter och åtkomst - vem som får komma åt vad. Kompletterar kryptering; ingen av dem ersätter den andra.

**Integritet**
: Garantin att innehållet är exakt detsamma som när det signerades eller skickades, utan att en enda bit ändrats. En av de två egenskaper en digital signatur ger oss.

**IPsec**
: Ett äldre protokoll som krypterar trafik på nätverksnivå, ofta mellan datacenter eller nätverkssegment.

**Isolering**
: Att begränsa vad en komprometterad process kan nå, med hjälp av containerisering, virtualisering och nätverkssegmentering. Ett av skydden för data in use.

**Klartext**
: Data i oläslig, okrypterad form - alltså läsbar för den som kommer åt den. Motsatsen till krypterad data.

**KMS (Key Management Service)**
: En dedikerad tjänst för att skapa, lagra, rotera och styra åtkomsten till kryptografiska nycklar. Nycklarna lämnar aldrig tjänsten i klartext. En KMS är *tjänsten*, till skillnad från en HSM som är *hårdvaran*.

**Kryptering**
: Att göra information oläslig för alla utom de som har rätt nyckel. En tvåvägsfunktion - vi krypterar och dekrypterar.

**Least privilege (minsta möjliga åtkomst)**
: Principen att en identitet eller process bara ska ha den åtkomst den faktiskt behöver, och bara så länge den behövs.

**MD5**
: En gammal hashalgoritm med kända svagheter. Ska inte användas för säkerhetsändamål. Förekommer fortfarande i gamla system, men vi ska inte lita på den.

**Mellanliggande CA (intermediate-certifikat)**
: Bryggan mellan rot-CA:n och servercertifikatet i certifikatkedjan. Signeras av rot-CA:n och kan återkallas utan att rot-certifikatet berörs.

**mTLS (mutual TLS)**
: TLS där båda parter verifierar varandras identitet, inte bara klienten som verifierar servern. Ett starkt skydd mellan tjänster i ett kluster.

**Nyckelpar**
: De två matematiskt kopplade nycklarna i asymmetrisk kryptering - en publik och en privat.

**Nyckelrotation**
: Att regelbundet byta ut nycklar för att begränsa skadan om en nyckel någon gång läcker. En KMS kan hantera flera nyckelversioner samtidigt så att rotation kan ske utan driftstopp.

**Objektlagringskryptering**
: Kryptering av objekt i objektlagring. Standard hos de flesta molnleverantörer i dag, ofta med en nyckel som tjänsten hanterar om vi inte anger en egen.

**OCSP (Online Certificate Status Protocol)**
: Ett protokoll för att i realtid kontrollera om ett certifikat återkallats. Ett alternativ till att ladda ner hela CRL-listan.

**OpenBao**
: Den öppna varianten av HashiCorp Vault. Ett populärt val i miljöer där vi inte vill låsa oss till en enskild leverantör.

**Privat nyckel**
: Den nyckel i ett nyckelpar som aldrig ska lämna sin ägare. En secret i ordets fulla bemärkelse. Läcker den måste allt kopplat till den återkallas.

**Publik nyckel**
: Den nyckel i ett nyckelpar som kan delas fritt. Den är inte hemlig - hela poängen är att den ska spridas.

**Rainbow table**
: En förberäknad tabell som en angripare kan använda för att snabbt slå upp lösenord från hashvärden. Motverkas av *salt*.

**Rot-CA (rot-certifikat)**
: Toppen av certifikathierarkin. Ett litet antal organisationer vars certifikat är inbyggda direkt i operativsystem och webbläsare. Skyddar sina privata nycklar extremt hårt och signerar sällan certifikat direkt.

**Salt**
: Ett slumpmässigt värde som läggs till ett lösenord innan det hashas. Gör att lika lösenord får olika hashvärden och omöjliggör rainbow table-attacker.

**scrypt**
: En långsam hashalgoritm för lösenordslagring, i samma familj som bcrypt och Argon2.

**Secret (hemlighet)**
: Information som ger åtkomst, bevisar identitet eller skyddar data, och som räcker i sig själv om den hamnar i fel händer. Till exempel lösenord, API-nycklar, privata nycklar och access tokens.

**Servercertifikat**
: Det certifikat en webbplats presenterar för webbläsaren. Signerat av en mellanliggande CA och giltigt för en specifik domän under en begränsad tid.

**SHA-1**
: En äldre hashalgoritm med kända svagheter. Ska inte användas för säkerhetsändamål.

**SHA-256**
: I dag den vanligaste hashalgoritmen för integritetskontroll och signaturer. En snabb algoritm - och just därför olämplig för lösenordslagring utan vidare.

**Signering**
: Att kombinera hashning och asymmetrisk kryptering för att bevisa ursprung (*autenticitet*) och att innehållet inte ändrats (*integritet*). Vi hashar innehållet och krypterar hashvärdet med vår privata nyckel.

**SQL-injection**
: En attack där en angripare manipulerar en databasfråga via applikationen. Eftersom applikationen är autentiserad dekrypterar och levererar databasen datan - krypteringen hjälper inte.

**SSH (Secure Shell)**
: Standardprotokollet för administrativ åtkomst till linux-servrar. Trafiken krypteras och autentisering sker helst med nyckelpar i stället för lösenord.

**Symmetrisk kryptering**
: Kryptering där samma nyckel används för att både kryptera och dekryptera. Snabb och effektiv, vilket gör den lämplig för data at rest och för själva dataflödet i TLS.

**TEE (Trusted Execution Environment)**
: En isolerad exekveringsmiljö i hårdvaran där kod körs skyddad även från operativsystemet och hypervisorn. Grunden för confidential computing.

**TLS (Transport Layer Security)**
: Protokollet som skyddar kommunikationen mellan klient och server. Ger kryptering, serverautentisering och integritet. Ligger bakom `https://`.

**TLS-handskakning**
: Inledningen av en TLS-anslutning, där parterna kommer överens om algoritmer, servern presenterar sitt certifikat och en gemensam symmetrisk nyckel etableras med hjälp av asymmetrisk kryptering.

**VPN (Virtual Private Network)**
: En krypterad tunnel mellan en klient och ett nätverk, så att en användare kan nå interna resurser som om hon satt på kontoret.

**Wildcard-certifikat**
: Ett certifikat som täcker alla subdomäner till en domän, till exempel `*.karoshi.se`.

**WireGuard**
: Ett modernare VPN-protokoll som är snabbare och enklare att konfigurera än äldre alternativ som OpenVPN och IPsec.

**Zero Trust**
: Ett säkerhetstänk som utgår från att inget nätverk är säkert i sig, inte ens det interna. Därför ska även intern trafik krypteras och varje begäran verifieras.

**Återkallelse**
: Att ogiltigförklara ett certifikat innan det gått ut, till exempel om den privata nyckeln komprometterats. Hanteras med CRL och OCSP.
