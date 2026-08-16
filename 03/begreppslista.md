# Begreppslista: IAM, Authorization & Zero Trust

> Detta dokument är AI-genererat och kan innehålla fel

Här samlar vi de begrepp vi stöter på i kapitlet, med korta förklaringar att slå upp i. Termerna står i bokstavsordning.

*AAA*
: *Authentication, Authorization* och *Accounting* (ibland *Auditing*). Ett samlingsbegrepp för de tre grundstenarna i åtkomstkontroll: bevisa vem du är, avgöra vad du får göra, och logga vad du gjorde.

*ABAC* (*Attribute-Based Access Control*)
: Attributbaserad åtkomstkontroll. Beslutet fattas utifrån en samling *attribut* hos identiteten, resursen, åtgärden och omgivningen tillsammans. Ger fin kontroll men kräver mer arbete att designa och underhålla.

*Active Directory*
: Ett vanligt identitetssystem som hanterar inloggningar för datorer, e-post och interna system i en organisation. Kan federeras mot molntjänster.

*API-token*
: En lång, slumpmässigt genererad sträng som en tjänst eller applikation använder för att autentisera sig istället för ett lösenord. Kan begränsas i behörighet och förses med utgångsdatum. En läckt token är i sig själv beviset och kräver ingen ytterligare autentisering.

*Assume breach*
: Zero Trust-principen att utgå från att ett intrång redan har skett eller kommer att ske. Vi designar systemen för att begränsa skadan genom segmentering, detektering och snabb respons.

*Attribut*
: En egenskap som ABAC väger in i ett åtkomstbeslut, till exempel avdelning, resursens känslighet, tid på dygnet, nätverksplats eller enhetens säkerhetsstatus.

*Auktorisering* (*authorization*)
: Avgör vad en autentiserad identitet faktiskt får göra. Att vara inloggad ger inte automatiskt tillgång till allt.

*Autentisering* (*authentication*)
: Att bevisa att vi är den vi påstår oss vara, med till exempel lösenord, certifikat, engångskod, hårdvarutoken eller biometri.

*Conditional Access*
: Ett system där åtkomstbeslut fattas dynamiskt utifrån kontext - identitet, enhet, plats, tid, beteende och resursens känslighet - istället för en gång vid inloggning. Sättet Zero Trust realiseras i praktiken.

*Credentials*
: Samlingsnamn för de uppgifter en identitet använder för att autentisera sig, till exempel lösenord, tokens, certifikat och nycklar.

*Entra ID*
: Microsofts molnbaserade identitetssystem (tidigare Azure AD), som hanterar inloggningar och kan federeras mot molntjänster.

*Federation*
: Ett förtroende mellan organisationens eget identitetssystem och en molntjänst. Användaren loggar in med sitt vanliga organisationskonto, och molntjänsten litar på att autentiseringen gjorts korrekt. Bygger på protokoll som *SAML* och *OpenID Connect*.

*Granskning* (*auditing*)
: Att logga och spåra vad som faktiskt hände - vem loggade in, vilken resurs öppnades, vilken åtgärd utfördes. Nödvändigt för att upptäcka intrång och utreda incidenter.

*Grupp*
: En samling användare som vi tilldelar behörigheter gemensamt, istället för konto för konto. Gör det enkelt att ändra behörigheter för många på en gång.

*Hårdvarutoken*
: En fysisk enhet, till exempel en *YubiKey*, som används som MFA-faktor. Räknas som den starkaste formen eftersom den inte går att stjäla på distans.

*IAM* (*Identity and Access Management*)
: Systemet som avgör vem som är vem och vad de får göra. I molnet är identiteten den nya säkerhetsgränsen - inte nätverket eller platsen.

*Identifikation*
: Steget där vi anger vem vi påstår oss vara, till exempel ett användarnamn eller en e-postadress. Systemet vet ännu inte om det stämmer.

*Just-in-time-åtkomst*
: Åtkomst som begärs när den behövs, beviljas för en begränsad tid och sedan återkallas automatiskt. Minskar exponeringstiden dramatiskt jämfört med permanenta rättigheter.

*Least privilege*
: Principen om minsta möjliga behörighet: ge en identitet exakt de rättigheter den behöver för sitt uppdrag och inte mer. Gäller rättigheter, tid, omfattning och antal permanenta adminkonton.

*Långlivade credentials*
: Credentials utan utgångsdatum, till exempel en token som aldrig går ut eller ett certifikat som gäller i tio år. Bekväma men riskabla - de ger en angripare obegränsad tid om de läcker.

*Maskinidentitet*
: En identitet som tillhör ett system istället för en människa, till exempel en CI/CD-pipeline, en webbapplikation eller en Kubernetes-pod. Autentiserar sig via service accounts och API-tokens.

*MFA* (*Multi-Factor Authentication*)
: Autentisering som kombinerar minst två olika typer av bevis: något vi vet (lösenord), något vi har (telefon eller token) och något vi är (biometri). Den enskilt mest effektiva åtgärden för att skydda konton.

*OpenID Connect*
: Ett etablerat protokoll för federation och inloggning, som ligger bakom mönster som "Logga in med Google".

*Permissions* (behörigheter)
: De enskilda rättigheter en policy kan bevilja eller neka, från ingen åtkomst alls till full administratörskontroll. Kom ihåg att även läsrättigheter kan vara känsliga.

*Policy*
: En regeluppsättning som definierar vad som är tillåtet och förbjudet, och kopplar ihop identiteter med behörigheter. Svarar på vilken resurs, vilken åtgärd och under vilka villkor.

*Privilege creep*
: Fenomenet att rättigheter samlas på hög över tid, till exempel när roller utökas för att lösa akuta problem och aldrig kontrolleras igen. Motverkas med regelbunden granskning.

*RBAC* (*Role-Based Access Control*)
: Rollbaserad åtkomstkontroll. Behörigheter kopplas till roller, och roller till användare. Enkelt att administrera och granska, passar tydliga och stabila arbetsroller.

*Roll*
: En identitet utan fast ägare, som tilldelas tillfälligt för ett specifikt uppdrag. Kan liknas vid en arbetsuniform vi tar på oss och lämnar igen. Central för att ge tjänster och system tillfällig åtkomst.

*Rotation*
: Att byta ut credentials med jämna mellanrum, eller använda kortlivade credentials som upphör automatiskt. Minskar tidsfönstret en angripare har om en credential läcker.

*SAML*
: Ett etablerat protokoll för federation som låter en organisation logga in mot molntjänster med sitt eget identitetssystem.

*Secrets manager*
: En dedikerad tjänst för att lagra hemliga värden som tokens, lösenord, certifikat och nycklar. Kontrollerar vem som får hämta dem och loggar varje gång det sker. Hemligheter ska aldrig lagras direkt i kod eller konfigurationsfiler.

*Service account* (servicekonto)
: Ett konto som tillhör ett system eller en applikation istället för en person. Ofta farligare än vanliga konton eftersom det har permanent åtkomst, sällan använder MFA och är svårare att övervaka.

*SSO* (*Single Sign-On*)
: Användaren autentiserar sig en gång mot ett centralt identitetssystem och når sedan alla anslutna tjänster utan att logga in igen. Förenklar och centraliserar, men koncentrerar risken - därför är MFA på SSO-kontot särskilt viktigt.

*Användare*
: En namngiven identitet kopplad till en specifik person. Varje person ska ha sitt eget konto, annars försvinner spårbarheten.

*Verify explicitly*
: Zero Trust-principen att alltid verifiera utifrån faktiska bevis - vem, vilken enhet, varifrån, hur färsk sessionen är - och göra det kontinuerligt, inte bara vid inloggning.

*Zero Trust*
: Ett säkerhetstänk, inte en produkt. Vi litar inte automatiskt på nätverket, platsen eller en gammal inloggning. Varje åtkomst ska kunna motiveras, kontrolleras och begränsas, varje gång. Vilar på principerna *verify explicitly*, *least privilege* och *assume breach*.
