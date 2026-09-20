# Lab 03 – Cisco IOS Basics & SSH Remote Access

## Ziel des Labs

In diesem Lab wird ein Cisco-Router grundlegend konfiguriert und anschließend per SSH von einem PC aus administriert.

Dabei werden wichtige Cisco-IOS-Grundlagen praktisch angewendet:

- IOS-Konfigurationsmodi
- Hostname setzen
- Router-Interface konfigurieren
- Console-Zugang absichern
- Privileged EXEC Mode mit `enable secret` schützen
- Passwörter in der Konfiguration verschleiern
- MOTD-Banner konfigurieren
- lokalen Benutzer erstellen
- SSH Version 2 aktivieren
- RSA-Schlüssel erzeugen
- VTY-Leitungen konfigurieren
- SSH-Verbindung von einem Client testen
- Running-Config dauerhaft als Startup-Config speichern

---

# 1. Topologie

Verwendete Geräte:

- 1x Cisco 1941 Router
- 1x Cisco 2960-24TT Switch
- 1x PC

Topologie:

```text
PC0 -------- Switch0 -------- R1
```

Verbindungen:

```text
PC0 FastEthernet0
        |
        |
Switch0 FastEthernet0/1

Switch0 FastEthernet0/2
        |
        |
R1 GigabitEthernet0/0
```

Für beide Verbindungen wird ein **Copper Straight-Through Kabel** verwendet.

---

# 2. IP-Adressierung

| Gerät | Interface | IP-Adresse | Subnetzmaske | Gateway |
|---|---|---:|---:|---:|
| PC0 | FastEthernet0 | 192.168.1.10 | 255.255.255.0 | 192.168.1.1 |
| R1 | GigabitEthernet0/0 | 192.168.1.1 | 255.255.255.0 | - |

Beide Geräte befinden sich damit im Netzwerk:

```text
192.168.1.0/24
```

---

# 3. Router starten

Beim ersten Start des Routers kann Cisco IOS fragen:

```text
Would you like to enter the initial configuration dialog? [yes/no]:
```

Hier wird eingegeben:

```text
no
```

Danach kann die Konfiguration manuell über die CLI durchgeführt werden.

---

# 4. Privileged EXEC Mode öffnen

Nach dem Start befindet man sich normalerweise im User EXEC Mode:

```text
Router>
```

Mit:

```text
enable
```

wechselt man in den Privileged EXEC Mode:

```text
Router#
```

Von hier aus können administrative Befehle ausgeführt und der Konfigurationsmodus geöffnet werden.

---

# 5. Global Configuration Mode

```text
configure terminal
```

Danach erscheint:

```text
Router(config)#
```

Der Global Configuration Mode wird verwendet, um die aktive Router-Konfiguration zu verändern.

---

# 6. Hostname konfigurieren

Der Router erhält den Namen `R1`.

```text
hostname R1
```

Der Prompt ändert sich danach von:

```text
Router(config)#
```

zu:

```text
R1(config)#
```

Der Hostname erleichtert besonders bei mehreren Routern und Switches die Identifikation eines Gerätes.

---

# 7. Router-Interface konfigurieren

Das Interface zum Switch ist:

```text
GigabitEthernet0/0
```

Konfiguration:

```text
interface gigabitEthernet 0/0
ip address 192.168.1.1 255.255.255.0
description LAN-to-Switch0
no shutdown
```

Mit:

```text
no shutdown
```

wird das Interface aktiviert.

Ohne diesen Befehl befindet sich ein Router-Interface normalerweise im Zustand:

```text
administratively down
```

Danach den Konfigurationsmodus verlassen:

```text
end
```

---

# 8. Interface überprüfen

Mit:

```text
show ip interface brief
```

kann der Status schnell kontrolliert werden.

Erwartetes Ergebnis:

```text
Interface              IP-Address      Status      Protocol
GigabitEthernet0/0     192.168.1.1    up          up
```

`up/up` bedeutet:

- physikalische Verbindung funktioniert
- Line Protocol funktioniert ebenfalls

---

# 9. PC0 konfigurieren

Auf PC0:

```text
Desktop
→ IP Configuration
```

Folgende Werte werden eingetragen:

```text
IP Address:      192.168.1.10
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.1.1
```

Der Default Gateway ist das Router-Interface im gleichen lokalen Netzwerk.

---

# 10. Verbindung testen

Auf PC0:

```text
Desktop
→ Command Prompt
```

Anschließend:

```text
ping 192.168.1.1
```

Erwartetes Ergebnis:

```text
Sent = 4
Received = 4
Lost = 0
```

Damit ist bestätigt, dass PC0 den Router erreichen kann.

---

# 11. Enable Secret konfigurieren

Der Zugang zum Privileged EXEC Mode wird mit einem Passwort geschützt.

Zurück in den Global Configuration Mode:

```text
configure terminal
```

Dann:

```text
enable secret Admin123
```

`enable secret` schützt den Wechsel von:

```text
R1>
```

zu:

```text
R1#
```

Die hier verwendeten Passwörter dienen nur diesem Packet-Tracer-Lab.

---

# 12. Passwort-Verschlüsselung aktivieren

```text
service password-encryption
```

Dieser Befehl sorgt dafür, dass viele Passwörter nicht mehr direkt als Klartext in der Konfiguration angezeigt werden.

Wichtig:

`service password-encryption` ist keine starke kryptographische Sicherheitslösung.

Der Befehl verhindert hauptsächlich, dass einfache Passwörter direkt lesbar in der Konfiguration stehen.

---

# 13. MOTD Banner konfigurieren

```text
banner motd #Authorized access only#
```

MOTD bedeutet:

```text
Message Of The Day
```

Das Banner wird Benutzern beim Zugriff auf das Gerät angezeigt.

Die Zeichen `#` markieren hier den Anfang und das Ende des Textes.

---

# 14. Console-Zugang konfigurieren

Die lokale Console-Leitung wird ausgewählt:

```text
line console 0
```

Danach befindet man sich im Line Configuration Mode:

```text
R1(config-line)#
```

Console-Passwort setzen:

```text
password Console123
```

Anschließend:

```text
login
```

Die vollständige Konfiguration lautet damit:

```text
line console 0
password Console123
login
```

Der Befehl:

```text
login
```

sorgt dafür, dass das konfigurierte Passwort beim Console-Zugriff tatsächlich abgefragt wird.

Danach:

```text
end
```

---

# 15. Lokalen Benutzer erstellen

Für die spätere SSH-Anmeldung wird ein lokaler Benutzer benötigt.

Global Configuration Mode öffnen:

```text
configure terminal
```

Benutzer erstellen:

```text
username Admin secret Bili123
```

Damit existiert jetzt ein lokales Benutzerkonto:

```text
Username: Admin
Password: Bili123
```

Auch diese Zugangsdaten werden ausschließlich für dieses Lab verwendet.

---

# 16. Domain Name konfigurieren

Für die Erzeugung der RSA-Schlüssel benötigt der Router einen Hostnamen und einen Domain Name.

Der Hostname `R1` wurde bereits gesetzt.

Jetzt:

```text
ip domain-name lab.local
```

Der vollständige Gerätename kann dadurch beispielsweise als:

```text
R1.lab.local
```

betrachtet werden.

---

# 17. RSA-Schlüssel erzeugen

SSH benötigt kryptographische Schlüssel.

```text
crypto key generate rsa
```

Cisco IOS fragt anschließend nach der Größe des RSA-Schlüssels:

```text
How many bits in the modulus [512]:
```

Für dieses Packet-Tracer-Lab wurde verwendet:

```text
1024
```

Danach erzeugt der Router das RSA-Schlüsselpaar.

---

# 18. SSH Version 2 aktivieren

```text
ip ssh version 2
```

SSH bedeutet:

```text
Secure Shell
```

SSH ermöglicht einen verschlüsselten Remote-Zugriff auf die Kommandozeile eines Netzwerkgerätes.

SSH sollte gegenüber Telnet bevorzugt werden, da Telnet Daten einschließlich Zugangsdaten unverschlüsselt übertragen kann.

---

# 19. VTY-Leitungen konfigurieren

VTY steht für:

```text
Virtual Teletype
```

VTY-Leitungen werden für Remote-Zugriffe wie SSH verwendet.

Die ersten fünf VTY-Leitungen werden ausgewählt:

```text
line vty 0 4
```

Danach:

```text
login local
```

Dieser Befehl sorgt dafür, dass die lokale Benutzer-Datenbank verwendet wird.

In diesem Lab ist das:

```text
username Admin secret Bili123
```

Anschließend wird nur SSH erlaubt:

```text
transport input ssh
```

Die komplette VTY-Konfiguration lautet:

```text
line vty 0 4
login local
transport input ssh
```

Damit wird Telnet für diese VTY-Leitungen nicht zugelassen.

---

# 20. SSH vom PC testen

Zuerst wird erneut überprüft, ob PC0 den Router erreichen kann:

```text
ping 192.168.1.1
```

Anschließend wird die SSH-Verbindung gestartet:

```text
ssh -l Admin 192.168.1.1
```

Dabei bedeutet:

```text
ssh
```

SSH-Verbindung starten.

```text
-l Admin
```

Login-Benutzer `Admin` verwenden.

```text
192.168.1.1
```

Zieladresse des Routers.

Danach wird das Passwort des lokalen Benutzers eingegeben.

Nach erfolgreicher Anmeldung erscheint:

```text
Authorized access only
```

und anschließend:

```text
R1>
```

Damit wird R1 jetzt remote von PC0 über SSH administriert.

---

# 21. Privileged EXEC Mode über SSH

Auch bei einer SSH-Verbindung landet der Benutzer zunächst im User EXEC Mode:

```text
R1>
```

Mit:

```text
enable
```

wird versucht, in den Privileged EXEC Mode zu wechseln.

Danach wird das zuvor konfigurierte Enable Secret abgefragt.

Nach erfolgreicher Anmeldung:

```text
R1#
```

Damit ist auch die administrative Verwaltung des Routers über SSH möglich.

---

# 22. Aktive Benutzer anzeigen

Mit:

```text
show users
```

werden aktuell angemeldete Benutzer angezeigt.

Im Lab war dabei unter anderem der Benutzer:

```text
Admin
```

auf einer VTY-Leitung sichtbar.

Das bestätigt, dass die Verbindung tatsächlich über einen Remote-VTY-Zugang aufgebaut wurde.

---

# 23. SSH-Status überprüfen

```text
show ip ssh
```

Im Lab wurde unter anderem angezeigt:

```text
SSH Enabled - version 2.0
Authentication timeout: 120 secs
Authentication retries: 3
```

Damit wird bestätigt, dass SSH aktiviert ist und Version 2 verwendet wird.

---

# 24. Running Configuration überprüfen

```text
show running-config
```

Damit wird die aktuell aktive Konfiguration im RAM angezeigt.

Wichtige Einträge dieses Labs sind beispielsweise:

```text
hostname R1
```

```text
enable secret ...
```

```text
username Admin secret ...
```

```text
ip ssh version 2
ip domain-name lab.local
```

Router-Interface:

```text
interface GigabitEthernet0/0
 description LAN-to-Switch0
 ip address 192.168.1.1 255.255.255.0
```

Console-Konfiguration:

```text
line console 0
 password ...
 login
```

VTY-Konfiguration:

```text
line vty 0 4
 login local
 transport input ssh
```

---

# 25. Running-Config und Startup-Config

Cisco-Geräte unterscheiden zwischen der aktuell verwendeten und der dauerhaft gespeicherten Konfiguration.

## Running-Config

Die aktuell aktive Konfiguration befindet sich im RAM.

Anzeige:

```text
show running-config
```

Änderungen an der Running-Config wirken sofort.

Nach einem Neustart würden diese Änderungen jedoch verloren gehen, wenn sie nicht gespeichert wurden.

## Startup-Config

Die Startup-Config enthält die Konfiguration, die beim Start des Gerätes geladen wird.

Anzeige:

```text
show startup-config
```

---

# 26. Konfiguration speichern

Die aktuelle Running-Config wird in die Startup-Config kopiert:

```text
copy running-config startup-config
```

Cisco IOS fragt:

```text
Destination filename [startup-config]?
```

Hier kann einfach Enter gedrückt werden.

Erwartete Ausgabe:

```text
Building configuration...
[OK]
```

Damit ist die Konfiguration dauerhaft gespeichert.

---

# 27. Wichtige IOS-Modi

Während des Labs wurden mehrere Cisco-IOS-Modi verwendet.

## User EXEC Mode

```text
R1>
```

Eingeschränkter Zugriff.

---

## Privileged EXEC Mode

```text
R1#
```

Erweiterte administrative Befehle.

Wechsel mit:

```text
enable
```

---

## Global Configuration Mode

```text
R1(config)#
```

Öffnen mit:

```text
configure terminal
```

Hier werden globale Geräteeinstellungen verändert.

---

## Interface Configuration Mode

```text
R1(config-if)#
```

Beispiel:

```text
interface gigabitEthernet 0/0
```

Hier werden Einstellungen eines Interfaces verändert.

---

## Line Configuration Mode

```text
R1(config-line)#
```

Beispielsweise für:

```text
line console 0
```

oder:

```text
line vty 0 4
```

---

# 28. Navigation zwischen den IOS-Modi

Eine Ebene zurück:

```text
exit
```

Direkt zurück zum Privileged EXEC Mode:

```text
end
```

Beispiel:

```text
R1(config-line)#
```

mit:

```text
end
```

wird direkt zu:

```text
R1#
```

---

# 29. Zentrale Befehle des Labs

| Befehl | Funktion |
|---|---|
| `enable` | Wechsel in den Privileged EXEC Mode |
| `configure terminal` | Global Configuration Mode öffnen |
| `hostname R1` | Gerätenamen setzen |
| `interface gigabitEthernet 0/0` | Interface konfigurieren |
| `ip address ...` | IPv4-Adresse konfigurieren |
| `no shutdown` | Interface aktivieren |
| `description ...` | Interface beschreiben |
| `enable secret ...` | Privileged EXEC Mode schützen |
| `service password-encryption` | einfache Passwörter in der Config verschleiern |
| `banner motd ...` | MOTD-Banner konfigurieren |
| `line console 0` | Console-Leitung konfigurieren |
| `password ...` | Line-Passwort setzen |
| `login` | Passwortabfrage auf einer Line aktivieren |
| `username ... secret ...` | lokalen Benutzer erstellen |
| `ip domain-name ...` | Domain Name konfigurieren |
| `crypto key generate rsa` | RSA-Schlüssel erzeugen |
| `ip ssh version 2` | SSH Version 2 aktivieren |
| `line vty 0 4` | Remote-Zugangsleitungen konfigurieren |
| `login local` | lokale Benutzer-Datenbank verwenden |
| `transport input ssh` | nur SSH als Remote-Protokoll erlauben |
| `show users` | aktive Benutzer anzeigen |
| `show ip ssh` | SSH-Status anzeigen |
| `show ip interface brief` | Interface-Übersicht anzeigen |
| `show running-config` | aktive Konfiguration anzeigen |
| `show startup-config` | gespeicherte Startkonfiguration anzeigen |
| `copy running-config startup-config` | Konfiguration dauerhaft speichern |

---

# 30. Ergebnis

Das Lab wurde erfolgreich abgeschlossen.

PC0 kann R1 über das lokale Netzwerk erreichen und sich anschließend verschlüsselt per SSH am Router anmelden.

Dabei wurden sowohl lokaler Console-Zugriff als auch Remote-Zugriff abgesichert.

Die endgültige Verbindung sieht so aus:

```text
PC0
192.168.1.10
     |
     | Ethernet
     |
Switch0
     |
     | Ethernet
     |
R1 G0/0
192.168.1.1
```

Remote-Administration:

```text
PC0
 |
 | SSH
 v
R1
```

Damit wurden grundlegende Cisco-IOS-Konfiguration, Zugriffssteuerung und Remote-Administration praktisch umgesetzt.
