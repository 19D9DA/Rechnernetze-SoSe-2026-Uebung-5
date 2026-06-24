# Rechnernetze 2026-SS - Übungsblatt 04

`#19D9DA - s4joregn`

## Aufgabe 1: Protokoll-Header

## Aufgabe 1: SSH Setup

In Ihrem Email-Postfach sollte Sie eine Mail erreicht haben, in welcher Sie Ihre persönlichen Zugangsdaten
zu einem Benutzerkonto auf dem Server 2026s.ag-syssoft.org finden. Melden Sie sich mit ssh auf dem
Server an. Hat Sie keine Email erreicht, dann melden Sie sich zeitnah bei mir! (s4llkohl@uni-trier.de)

## Aufgabe 2: SSH Login mit Schlüsselpaar

Ersetzen Sie den Login mit Passwort durch einen Login ohne Passwort mit einem SSH-Keypair. Versuchen
Sie sich erneut anzumelden. Was hat sich verändert? Warum ist diese Art der Verschlüsselung sicherer als
ein Login mit einem beliebigen Passwort? Dokumentieren Sie Ihre Ergebnisse.

Auf dem Host Gerät:

```
ssh-keygen
```

Auf den Server übertragen

```
ssh-copy-id username@hostname
```

```
sudo nano /etc/ssh/sshd_config

PasswirdAuthentication yes
PasswirdAuthentication no

sudo systemctl restart ssh
```

### Vorteile

- Schutz vor Brute-Force
- Keine übertragung des Passwort im Netz
- Zwei Faktor Authentifizierung

## Aufgabe 3: SSH Basics und Nutzerberechtigungen

Erläutern Sie zu jeder Teilaufgabe, wie Sie vorgegangen sind, indem sie die konkreten Befehle angeben,
die Sie verwendet haben.

### a) Erstellen Sie zwei Verzeichnisse 1 und 2 in Ihrem Benutzerverzeichnis.

```
mkdir ~/1
mkdir ~/2
```

### b) Erstellen Sie lokal (auf ihrem Gerät) eine kleine Textdatei und laden Sie Diese in das Verzeichnis 1 auf dem Server.

```
Lokal
echo "Hallo Server" > datei.txt

scp datei.txt username@hostname:~/1/
```

### c) Erstellen Sie direkt auf dem Server im Verzeichnis 2 ein kleines Shellscript, welches bei Ausführung eine einzelne Zeichenkette ausgibt und versuchen Sie das Script auszuführen.

```
nano script.sh

#!/bin/bash
echo "Hallo ich bin ein Skript!""
```

### d) Verändern Sie die Berechtigungen des Scripts so, dass:

    1. der Besitzer das Script lesen, schreiben und ausführen kann,
    2. andere Gruppenmitglieder das Script lesen und ausführen können und
    3. andere Nutzer das Script nur ausführen können. Überprüfen Sie die gesetzten Berechtigungen mit ls -l. Geben Sie den kompletten Listeneintrag an und erklären Sie woran man die veränderten Berechtigungen erkennen kann.

```
Besitzer (u): lesen (4) + schreiben (2) + ausführen (1) = 7
Gruppe (g): lesen (4) + ausführen (1) = 5
Andere (o): ausführen (1) = 1
chmod 751 script.sh

ls -l script.sh
```


```
-rwxr-x--x 1 s4joregn s4joregn 44 Jun 24 20:49 script.sh

Block -rwxr-x--x zeigt die Rechte exakt an:
rwx : Besitzer darf lesen (r), schreiben (w) und ausführen (x)
r-x : Gruppe darf lesen (r), nicht schreiben (-) und ausführen (x)
--x : Nutzer dürfen nicht lesen (-), nicht schreiben (-), aber ausführen (x)
```
## Aufgabe 4: Dateidownload per SSH

Laden Sie die Fachprüfungsordnung der Informatik von 2020 aus dem Internet herunter und speichern Sie
diese als FPO2020.pdf in Ihrem Home Verzeichnis auf dem Server. Sie finden die Ordnung auf dem Server
ordnungen.uni-trier.de unter dem Namen FPO_BSc_1F-HF-NF_Informatik_2020-07-27.pdf. Bearbeiten Sie
diese Aufgabe direkt auf dem Server, nutzen Sie dafür wget oder curl und NICHT scp. Geben Sie alle
verwendeten Befehle an.

```
wget https://www.uni-trier.de/fileadmin/fb4/INF/BA_MA/FPO_BSc_1F-HF-NF_Informatik_2020-07-27.pdf

```

## Aufgabe 5: SSH Portweiterleitung (Tunneling)

Auf dem Server läuft auf Port 3000 ein Webserver der nur vom Server aus erreichbar ist. Stellen Sie eine
SSH Verbindung mit lokaler Portweiterleitung her, sodass der Webserver auf Port 3000 des Servers auf
Ihrem Gerät unter der Adresse localhost:8080 über den Browser erreichbar ist. Erklären Sie wie Sie
vorgegangen sind und beschreiben Sie den Datenfluss zwischen Browser, SSH-Tunnel und Server.


```
ssh -L 8080:localhost:3000 benutzername@hostname
```

```
ssh -L: Aktiviert lokale Local Port Forwarding
8080: Port auf lokalen Gerät. SSH-Client öffnet Port und hört auf eingehende Verbindungen.
localhost:3000: Sobald Daten an Ihrem lokalen Port 8080 ankommen, werden sie verschlüsselt durch den SSH-Tunnel transportiert. Der SSH-Server auf der Gegenseite packt die Daten aus und leitet sie an Server localhost Port 3000 weiter.
Im Browser http://localhost:8080 leitet SSH die Anfrage an den Webserver (Port 3000) des Remote-Servers weiter.
```