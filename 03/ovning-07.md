# Övning - Utvärdera MFA-metoder

Materialet slår fast att MFA är den enskilt mest effektiva åtgärden för att skydda konton, men också att olika metoder har olika styrka. Här får vi jämföra metoderna och matcha dem mot olika situationer.

## Bakgrund

De tre metoderna vi jämför är:

- *engångskod via SMS*
- *autentiseringsapp* på telefonen (TOTP)
- *hårdvarutoken*, till exempel en YubiKey

## Uppgift

Vi fyller i en jämförelse av de tre metoderna. För varje metod beskriver vi:

- hur den fungerar i korthet
- dess starkaste sida
- dess svagaste sida, alltså hur en angripare skulle kunna kringgå den
- hur bekväm den är för användaren

## Matcha metod mot situation

Sedan väljer vi en lämplig metod för var och en av dessa situationer och motiverar valet:

- En vanlig anställd som loggar in på interna verktyg.
- En administratör som förvaltar hela molnmiljön.
- Ett servicekonto som körs helt utan människa inblandad.

> Den sista situationen är en liten fälla värd att stanna vid. Ett servicekonto kan inte trycka på en MFA-knapp. Vad använder vi i stället för att skydda en maskinidentitet? Här får vi gärna knyta an till det materialet säger om kortlivade tokens och begränsad åtkomst.

## Redovisning

En jämförelsetabell över de tre metoderna, plus en kort motivering för varje situation.

> **För Väl Godkänt:** Vi förklarar varför SMS är svagast av de tre, med begrepp som *SIM-swapping* och resonerar kring varför det ändå är bättre än att inte ha någon andra faktor alls. Vi ger också ett genomtänkt svar på hur maskinidentiteter skyddas när MFA inte är möjligt.

