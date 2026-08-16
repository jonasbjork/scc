# Övning - Designa IAM på papper

Nu sätter vi ihop byggstenarna från materialet - användare, grupper, roller, policies och behörigheter - till en sammanhängande design för en påhittad organisation. Vi skriver ingen kod. Vi tänker, ritar och motiverar.

## Organisationen

*Molnbyrån AB* är ett litet företag som bygger webbtjänster åt kunder. De har:

- två utvecklare
- en drifttekniker
- en person som sköter ekonomi och fakturor
- en extern konsult som är inne i tre veckor för ett enskilt projekt
- en automatiserad *CI/CD-pipeline* som bygger och driftsätter kod varje natt
- en backuptjänst som läser filer nattetid och skriver dem till ett separat lagringsutrymme

## Uppgift

Vi ritar upp en IAM-design för Molnbyrån. Designen ska innehålla:

- vilka *användare* som finns och principen om ett konto per person
- vilka *grupper* eller *roller* vi skapar och vilka behörigheter var och en får
- hur vi hanterar de två *maskinidentiteterna* (pipelinen och backuptjänsten)
- hur vi hanterar *konsulten* som bara ska vara med en kort tid
- var vi väljer att kräva MFA

## Att tänka på

- Vilken är den lägsta behörighetsnivå som faktiskt räcker för varje identitet?
- Hur ser vi till att konsultens åtkomst inte lever kvar efter tre veckor?
- Vad får CI/CD-pipelinen absolut inte kunna göra, även om det vore bekvämt?

## Redovisning

En enkel tabell eller punktlista som visar varje identitet, dess roll och dess behörigheter, samt ett kort resonemang om de val vi gjort.
