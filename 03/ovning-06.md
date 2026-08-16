# Övning - Skriv om till Zero Trust

Materialet beskriver hur det gamla perimetertänket inte längre räcker och ställer upp Zero Trusts tre principer: *verify explicitly*, *least privilege* och *assume breach*. I den här övningen tar vi en gammaldags miljöbeskrivning och skriver om den.

## Utgångsläget

Läs följande beskrivning av en organisation som fortfarande tänker i perimeter:

> "Alla våra servrar står i vårt eget serverrum, bakom vår brandvägg. Är man innanför brandväggen, alltså på kontorsnätverket eller inkopplad via VPN, så litar vi på användaren. Då kommer man åt de flesta interna system utan att logga in igen. De anställda loggar in på morgonen och är sedan betrodda hela dagen. Administratörerna har permanenta adminkonton som de använder till allt. Vi har en stark brandvägg, så vi känner oss trygga."

## Uppgift

Vi skriver om organisationens säkerhetsupplägg så att det följer Zero Trust. För varje av de tre principerna beskriver vi konkret vad som förändras.

- **Verify explicitly:** Vad slutar vi att lita på automatiskt, och vad börjar vi verifiera i stället, och hur ofta?
- **Least privilege:** Vad gör vi åt de permanenta adminkontona och den breda åtkomsten innanför brandväggen?
- **Assume breach:** Hur designar vi om miljön för att begränsa skadan när en angripare redan är inne?

## Att besvara

- Vilken enskild mening i utgångstexten är farligast ur ett Zero Trust-perspektiv, och varför?
- Var i den nya designen passar *Conditional Access* in?

## Redovisning

En omskriven beskrivning av organisationen, plus en kort punktlista över de viktigaste förändringarna.

> **För Väl Godkänt:** Vi tar med ett konkret exempel i stil med materialets analytiker: samma identitet och samma lösenord, men olika kontext ska ge olika beslut. Vi beskriver ett fall där åtkomst beviljas och ett där den nekas eller kräver ytterligare verifiering.
