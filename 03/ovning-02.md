# Övning - Användare, grupper och sudo i en container

I den här övningen kommer vi:

- skapa användare och grupper i en Fedora-container
- ge en grupp rätt att köra administrativa kommandon
- se skillnaden mellan ett vanligt konto och ett administrativt konto i praktiken

Det här är rollbaserad åtkomstkontroll på Linuxnivå och det knyter direkt an till principen om att separera vardagskonto och adminkonto.

## Starta miljön

Vi startar en Fedora-container och kliver in i den:

```console
docker run -it --name linux-iam fedora:44 /bin/bash
```

`-it`
: ger oss en interaktiv terminal inne i containern.

`--name linux-iam`
: namnet på containern.

`fedora:44`
: den avbild vi utgår från.

Vi installerar verktygen vi behöver:

```console
dnf install -y sudo passwd su
```

## Skapa användare och grupper

Vi skapar en grupp för utvecklare och en användare som hör hemma där:

```console
groupadd utvecklare
useradd -m -G utvecklare anna
```

`groupadd utvecklare`
: skapar gruppen *utvecklare*.

`-m`
: skapar en hemkatalog åt användaren.

`-G utvecklare`
: lägger användaren i gruppen *utvecklare* utöver sin egen grupp.

`anna`
: användarnamnet.

Vi ger Anna ett lösenord:

```console
passwd anna
```

På Fedora, som på de flesta Linuxsystem, är det gruppen *wheel* som ger rätt att köra `sudo`. Nu skapar vi ett separat administratörskonto och lägger det i den gruppen:

```console
useradd -m -G wheel driftadmin
passwd driftadmin
```

Här har vi alltså två helt olika konton: *anna* för det dagliga arbetet och *driftadmin* för administrativa uppgifter.

## Prova skillnaden

Vi loggar in som Anna:

```console
su - anna
```

Vi försöker göra något som kräver administratörsrätt, till exempel installera ett program:

```console
sudo dnf install -y tree
```

Anna nekas, eftersom hon inte är med i *wheel*. Det är precis meningen. Vi lämnar Annas session:

```console
exit
```

Nu provar vi samma sak som driftadmin:

```console
su - driftadmin
sudo dnf install -y tree
```

Den här gången går det, efter att vi angett lösenordet.

> När vi städar upp efter labben, tänk på att `userdel` tar bort ett konto men att vi behöver `userdel -r` för att också ta bort hemkatalogen. Materialet påminner oss om att gamla, kvarlämnade konton är en risk - det gäller även i det lilla. Det går också bra att radera containern för att städa upp.

## Reflektion och redovisning

- Varför är det en dålig idé att sköta både e-post, webbläsande och serveradministration från *samma* konto?
- Vad är fördelen med att koppla administrativ rätt till en *grupp* i stället för till varje enskild användare?
- Hur hänger det vi gjorde här ihop med principen om least privilege?

