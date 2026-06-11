# BBB-LPIC Lernprojekt
## Lerne LPIC1+2 praktisch am BeagleBone Black
### Ziel: LPIC Prüfung bestehen durch echte Praxis!

---

## Projektübersicht

Du baust einen **Mini-Firmenserver** auf dem BeagleBone Black.
Jeder LPIC-Lernblock wird direkt auf dem BBB praktisch umgesetzt.
Alles wird mit **Git + VS Code** dokumentiert – dein erstes Portfolio!

### Was entsteht am Ende:
- Vollständig konfigurierter Linux Server auf dem BBB
- Benutzer- und Gruppenverwaltung wie in einer echten Firma
- Webserver, Mailserver, Firewall
- RFID Zugangskontrolle mit LEDs (Embedded Bonus!)
- Git Repository mit kompletter Dokumentation
- Portfolio für australische Arbeitgeber! 🇦🇺

---

## Git Repository Struktur

```
bbb-lpic-projekt/
├── README.md
├── tag1-grundlagen/
│   ├── aufgaben.md
│   └── loesungen.md
├── tag2-benutzer/
│   ├── aufgaben.md
│   └── loesungen.md
├── tag3-partitionen/
│   ├── aufgaben.md
│   └── loesungen.md
├── tag4-permissions/
│   ├── aufgaben.md
│   └── loesungen.md
├── tag5-netzwerk/
│   ├── aufgaben.md
│   └── loesungen.md
├── scripts/
│   ├── backup.sh
│   ├── user-erstellen.sh
│   ├── monitoring.sh
│   └── tool.sh
└── hardware/
    ├── rfid-zugangskontrolle.md
    └── led-status.md
```

---

## TAG 1 – Linux Grundlagen & Dateisystem

### Lernziele:
- Grundbefehle sicher beherrschen
- Dateisystem verstehen
- Archive erstellen und entpacken
- Umleitung und Pipes nutzen

### Projekt-Aufgabe auf dem BBB:

**Schritt 1: Projektstruktur anlegen**
```bash
# Verbinde dich per SSH mit dem BBB
ssh terence@192.168.7.2

# Firmen-Verzeichnisstruktur erstellen
mkdir -p /firma/{buchhaltung,it,produktion,logs,backup}
mkdir -p /firma/it/{scripts,config,docs}
mkdir -p /firma/logs/{system,web,mail}

# Verzeichnisstruktur prüfen
ls -la /firma/
find /firma -type d
```

**Schritt 2: Dateien erstellen und verwalten**
```bash
# Firmendaten erstellen
touch /firma/buchhaltung/rechnung{1..10}.txt
touch /firma/produktion/bericht{1..5}.txt
touch /firma/it/docs/handbuch.txt

# Daten in Dateien schreiben
cat > /firma/it/docs/handbuch.txt
BBB Firmenserver - IT Handbuch
Erstellt von: terence
Datum: $(date)
^C

# Datei anzeigen
cat /firma/it/docs/handbuch.txt

# Daten anhängen
echo "Version: 1.0" >> /firma/it/docs/handbuch.txt
```

**Schritt 3: Suchen und Finden**
```bash
# Alle .txt Dateien finden
find /firma -name "*.txt"

# Alle Dateien größer als 1KB
find /firma -size +1k

# Logs älter als 7 Tage (später für Cronjob!)
find /firma/logs -mtime +7
```

**Schritt 4: Backup mit tar erstellen**
```bash
# Tägliches Backup Script erstellen
cat > /firma/it/scripts/backup.sh
#!/bin/bash
DATUM=$(date +%Y-%m-%d)
BACKUP_DIR=/firma/backup

tar -czf $BACKUP_DIR/firma-backup-$DATUM.tar.gz /firma/buchhaltung /firma/produktion
echo "Backup erstellt: firma-backup-$DATUM.tar.gz"

^C

chmod u+x /firma/it/scripts/backup.sh
./firma/it/scripts/backup.sh

# Backup prüfen
ls -lh /firma/backup/
tar -tf /firma/backup/firma-backup-*.tar.gz
```

**Schritt 5: Umleitung und Logs**
```bash
# Fehler in Logdatei umleiten
find / -name "*.conf" 2>/dev/null > /firma/logs/system/config-dateien.txt

# Log anzeigen
cat /firma/logs/system/config-dateien.txt

# Systeminfo in Datei schreiben
uname -a > /firma/logs/system/system-info.txt
date >> /firma/logs/system/system-info.txt
df -h >> /firma/logs/system/system-info.txt
```

### Git Commit nach Tag 1:
```bash
git add .
git commit -m "Tag 1: Firmenstruktur angelegt, Backup Script erstellt"
```

---

## TAG 2 – Benutzer, Gruppen & sudo

### Lernziele:
- Benutzer erstellen und verwalten
- Gruppen und Berechtigungen
- sudo konfigurieren
- Passwörter und Sicherheit

### Projekt-Aufgabe auf dem BBB:

**Die Firma bekommt echte Mitarbeiter!**

**Schritt 1: Abteilungsgruppen erstellen**
```bash
# Gruppen für jede Abteilung
groupadd buchhaltung
groupadd it-abteilung
groupadd produktion
groupadd management

# Gruppen prüfen
tail /etc/group
```

**Schritt 2: Mitarbeiter anlegen**
```bash
# IT Mitarbeiter (darf sudo!)
useradd -c "IT Administrator" -g it-abteilung admin1
passwd admin1

# Buchhaltung
useradd -c "Buchhalterin" -g buchhaltung user-buch1
passwd user-buch1

# Produktion
useradd -c "Produktionsleiter" -g produktion user-prod1
passwd user-prod1

# Chef (in allen Gruppen!)
useradd -c "Geschaeftsfuehrer" chef
usermod -aG buchhaltung,it-abteilung,produktion,management chef
passwd chef
```

**Schritt 3: sudo Rechte vergeben**
```bash
# IT Abteilung darf alles als root
vim /etc/sudoers.d/it-abteilung
%it-abteilung ALL=(root) ALL

# Buchhaltung darf nur bestimmte Befehle
vim /etc/sudoers.d/buchhaltung
%buchhaltung ALL=(root) /bin/ls, /bin/cat
```

**Schritt 4: Benutzer Script erstellen**
```bash
cat > /firma/it/scripts/user-erstellen.sh
#!/bin/bash
echo "=== BBB Firmenmitarbeiter Verwaltung ==="
echo ""
echo "Wähle Abteilung:"
echo "1) IT"
echo "2) Buchhaltung"
echo "3) Produktion"
read -p "Deine Wahl: " abteilung

read -p "Benutzername: " username
read -p "Vollständiger Name: " fullname

case $abteilung in
    1) gruppe="it-abteilung" ;;
    2) gruppe="buchhaltung" ;;
    3) gruppe="produktion" ;;
    *) echo "Ungültige Wahl!"; exit 1 ;;
esac

useradd -c "$fullname" -g $gruppe $username
echo "Benutzer $username in Gruppe $gruppe erstellt!"
^C

chmod u+x /firma/it/scripts/user-erstellen.sh
```

### Git Commit nach Tag 2:
```bash
git add .
git commit -m "Tag 2: Firmenmitarbeiter und Gruppen eingerichtet"
```

---

## TAG 3 – Partitionen & Dateisysteme

### Lernziele:
- Partitionen erstellen mit parted
- Dateisysteme formatieren
- Mounten und fstab
- Swap verwalten

### Projekt-Aufgabe auf dem BBB:

**BBB bekommt eine SD-Karte als Datenspeicher!**

**Schritt 1: SD-Karte vorbereiten**
```bash
# Verfügbare Geräte prüfen
lsblk
parted /dev/mmcblk0 print free

# Neue Partition für Firmendaten erstellen
parted /dev/mmcblk1 mklabel gpt
parted /dev/mmcblk1 mkpart firmadaten ext4 1M 2G
parted /dev/mmcblk1 mkpart firmaswap linux-swap 2G 2.5G
parted /dev/mmcblk1 print
lsblk
```

**Schritt 2: Formatieren und mounten**
```bash
# Formatieren
mkfs.ext4 /dev/mmcblk1p1
mkswap /dev/mmcblk1p2

# Mountpunkt erstellen
mkdir /firmadaten

# Mounten
mount /dev/mmcblk1p1 /firmadaten
swapon /dev/mmcblk1p2

# Prüfen
lsblk
df -h /firmadaten
free -h
```

**Schritt 3: Persistent mounten**
```bash
# UUID ermitteln
blkid /dev/mmcblk1p1
blkid /dev/mmcblk1p2

# fstab editieren
nano /etc/fstab
UUID=DEINE-UUID /firmadaten ext4 defaults,nofail 0 0
UUID=DEINE-UUID swap swap defaults 0 0

# Testen
systemctl daemon-reload
mount -a
lsblk
```

### Git Commit nach Tag 3:
```bash
git add .
git commit -m "Tag 3: SD-Karte als Firmenspeicher eingerichtet"
```

---

## TAG 4 – Permissions, Scripts & Services

### Lernziele:
- Dateiberechtigungen verstehen
- SUID, SGID, Sticky Bit
- Shell Scripts schreiben
- Services verwalten

### Projekt-Aufgabe auf dem BBB:

**Abteilungsordner absichern + Admin Tool Script!**

**Schritt 1: Ordner absichern**
```bash
# Abteilungsordner richtig berechtigen
chown root:buchhaltung /firma/buchhaltung
chown root:it-abteilung /firma/it
chown root:produktion /firma/produktion

# Nur eigene Abteilung darf lesen/schreiben
chmod 770 /firma/buchhaltung
chmod 770 /firma/it
chmod 770 /firma/produktion

# SGID setzen - neue Dateien erben Gruppe!
chmod g+s /firma/buchhaltung
chmod g+s /firma/it
chmod g+s /firma/produktion

# Sticky Bit auf logs - nur Owner darf löschen
chmod +t /firma/logs

# Prüfen
ls -ld /firma/buchhaltung
ls -ld /firma/logs
```

**Schritt 2: Admin Tool Script (aus LPIC Schulung!)**
```bash
cat > /firma/it/scripts/tool.sh
#!/bin/bash
while true
do
    echo ""
    echo "=== BBB Firmenserver Admin Tool ==="
    echo ""
    echo "1) Service starten"
    echo "2) Service stoppen"
    echo "3) Service Status"
    echo "4) Festplatten Info"
    echo "5) System Info"
    echo "6) Benutzer anlegen"
    echo "7) Backup starten"
    echo "8) Logs anzeigen"
    echo "9) Beenden"
    echo ""
    read -p "Wahl (1-9): " choice

    case $choice in
        1)
            read -p "Service Name: " service
            systemctl start $service
            echo "$service gestartet"
            ;;
        2)
            read -p "Service Name: " service
            systemctl stop $service
            echo "$service gestoppt"
            ;;
        3)
            read -p "Service Name: " service
            systemctl status $service
            ;;
        4)
            echo "=== Festplatten Info ==="
            df -h
            lsblk
            ;;
        5)
            echo "=== System Info ==="
            echo "Hostname: $(hostname)"
            echo "Uptime: $(uptime -p)"
            echo "Kernel: $(uname -r)"
            echo "CPU: $(cat /proc/cpuinfo | grep 'model name' | head -1 | cut -d: -f2)"
            echo "RAM:"
            free -h
            echo "Temperatur:"
            cat /sys/class/thermal/thermal_zone0/temp
            ;;
        6)
            /firma/it/scripts/user-erstellen.sh
            ;;
        7)
            /firma/it/scripts/backup.sh
            ;;
        8)
            echo "=== Letzte 20 Logeinträge ==="
            journalctl -n 20
            ;;
        9)
            echo "Auf Wiedersehen!"
            exit 0
            ;;
        *)
            echo "Ungültige Eingabe!"
    esac
    echo ""
    read -p "Enter drücken um fortzufahren..."
done
^C

chmod u+x /firma/it/scripts/tool.sh
cp /firma/it/scripts/tool.sh /usr/local/bin/firmantool
```

**Schritt 3: Webserver als Firmen-Intranet**
```bash
# Apache installieren
apt install apache2 -y
systemctl enable apache2
systemctl start apache2

# Intranet Seite erstellen
cat > /var/www/html/index.html
<!DOCTYPE html>
<html>
<head><title>BBB Firmenserver Intranet</title></head>
<body>
<h1>Willkommen im Firmennetzwerk!</h1>
<p>Server: BeagleBone Black</p>
<p>Datum: $(date)</p>
</body>
</html>
^C

# Status prüfen
systemctl status apache2
```

### Git Commit nach Tag 4:
```bash
git add .
git commit -m "Tag 4: Berechtigungen gesetzt, Admin Tool und Webserver eingerichtet"
```

---

## TAG 5 – Netzwerk, Cron, Logs & Firewall

### Lernziele:
- Netzwerk konfigurieren
- Cronjobs einrichten
- Logs mit journalctl analysieren
- Firewall konfigurieren

### Projekt-Aufgabe auf dem BBB:

**Server wird abgesichert und automatisiert!**

**Schritt 1: Cronjobs einrichten**
```bash
crontab -e

# Tägliches Backup um 2 Uhr nachts
0 2 * * * /firma/it/scripts/backup.sh >> /firma/logs/system/backup.log 2>&1

# Stündlich System-Info loggen
0 * * * * echo "$(date): $(uptime)" >> /firma/logs/system/uptime.log

# Alte Logs wöchentlich löschen (älter als 30 Tage)
0 3 * * 0 find /firma/logs -mtime +30 -delete

# Cronjobs prüfen
crontab -l
```

**Schritt 2: Logs analysieren**
```bash
# Alle Logs anzeigen
journalctl

# Nur Fehler
journalctl -p err

# Letzte Stunde
journalctl --since "1 hour ago"

# Apache Logs
journalctl -u apache2

# Echtzeit verfolgen
journalctl -f
```

**Schritt 3: Firewall einrichten**
```bash
# ufw installieren und aktivieren
apt install ufw -y
ufw enable

# Nur nötige Ports öffnen
ufw allow ssh          # SSH Zugang
ufw allow 80/tcp       # Webserver
ufw allow from 192.168.7.0/24  # Nur vom Laptop

# Status prüfen
ufw status numbered
```

**Schritt 4: Monitoring Script**
```bash
cat > /firma/it/scripts/monitoring.sh
#!/bin/bash
LOG=/firma/logs/system/monitoring.log

echo "=== $(date) ===" >> $LOG
echo "CPU Last: $(top -bn1 | grep 'Cpu(s)' | awk '{print $2}')%" >> $LOG
echo "RAM: $(free -h | grep Mem | awk '{print $3"/"$2}')" >> $LOG
echo "Disk: $(df -h / | tail -1 | awk '{print $5}')" >> $LOG
echo "Temperatur: $(cat /sys/class/thermal/thermal_zone0/temp | awk '{print $1/1000"°C"}')" >> $LOG
echo "" >> $LOG

^C

chmod u+x /firma/it/scripts/monitoring.sh

# In Cronjob eintragen
crontab -e
*/5 * * * * /firma/it/scripts/monitoring.sh
```

### Git Commit nach Tag 5:
```bash
git add .
git commit -m "Tag 5: Firewall, Cronjobs und Monitoring eingerichtet"
```

---

## BONUS – RFID Zugangskontrolle (Embedded!)

### Hardware:
- RFID Leser (aus deinem Raspberry Pi Paket)
- LEDs (rot + grün)
- BeagleBone Black GPIO

### Idee:
- RFID Karte scannen → BBB prüft ob Benutzer berechtigt
- Grüne LED = Zugang erlaubt
- Rote LED = Zugang verweigert
- Alles wird geloggt in `/firma/logs/zugang.log`

### Script (später mit C optimieren!):
```bash
cat > /firma/it/scripts/rfid-zugang.sh
#!/bin/bash
LOG=/firma/logs/zugang.log
BERECHTIGTE_KARTEN=("KARTE123" "KARTE456")

# GPIO LEDs initialisieren
echo 67 > /sys/class/gpio/export  # Grüne LED GPIO
echo 68 > /sys/class/gpio/export  # Rote LED GPIO
echo out > /sys/class/gpio/gpio67/direction
echo out > /sys/class/gpio/gpio68/direction

while true
do
    read -p "Karte scannen: " karte_id
    
    if [[ " ${BERECHTIGTE_KARTEN[@]} " =~ " ${karte_id} " ]]; then
        echo "$(date): ZUGANG ERLAUBT - Karte: $karte_id" >> $LOG
        echo "✅ Zugang erlaubt!"
        echo 1 > /sys/class/gpio/gpio67/value  # Grün an
        echo 0 > /sys/class/gpio/gpio68/value  # Rot aus
        sleep 3
        echo 0 > /sys/class/gpio/gpio67/value  # Grün aus
    else
        echo "$(date): ZUGANG VERWEIGERT - Karte: $karte_id" >> $LOG
        echo "❌ Zugang verweigert!"
        echo 0 > /sys/class/gpio/gpio67/value  # Grün aus
        echo 1 > /sys/class/gpio/gpio68/value  # Rot an
        sleep 3
        echo 0 > /sys/class/gpio/gpio68/value  # Rot aus
    fi
done
^C

chmod u+x /firma/it/scripts/rfid-zugang.sh
```

### Git Commit Bonus:
```bash
git add .
git commit -m "Bonus: RFID Zugangskontrolle implementiert"
git push origin main
```

---

## Git Workflow – So dokumentierst du alles

### Einmalig einrichten:
```bash
git config --global user.name "Terence Hill"
git config --global user.email "deine@email.com"

mkdir ~/bbb-lpic-projekt
cd ~/bbb-lpic-projekt
git init
```

### Nach jeder Aufgabe:
```bash
git status          # Was hat sich geändert?
git add .           # Alle Änderungen vormerken
git commit -m "Beschreibung was du gemacht hast"
git push            # Zu GitHub hochladen
```

### Gute Commit-Nachrichten:
```
✅ Tag 1: Firmenstruktur und Backup Script
✅ Tag 2: 5 Benutzer und 4 Gruppen erstellt
✅ Tag 3: SD-Karte partitioniert und gemountet
✅ Tag 4: Berechtigungen und Admin Tool
✅ Tag 5: Firewall und Cronjobs
✅ Bonus: RFID Zugangskontrolle
```

---

## Nächste Schritte nach diesem Projekt

1. **Buildroot** – minimales Linux für BBB selbst bauen
2. **Yocto** – professionelles Embedded Build System
3. **Eigene Kernel Treiber** in C schreiben
4. **GitHub Portfolio** – für australische Arbeitgeber
5. **LPIC Prüfung** ablegen!

---

*Erstellt: Juni 2026 | BeagleBone Black | LPIC Lernprojekt*
*Ziel: Embedded Linux Spezialist & Australien 🇦🇺*
