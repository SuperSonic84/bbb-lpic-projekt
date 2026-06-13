##**Schritt 5: Umleitung und Logs**
```bash
# Fehler in Logdatei umleiten
find / -name "*.conf" 2>/dev/null > /firma/logs/system/config-
dateien.txt

# Log anzeigen
cat /firma/logs/system/config-dateien.txt
sudo find / -name "*.conf" 2>/dev/null | sudo tee /firma/logs/system/config-dateien.txt

# Systeminfo in Datei schreiben
uname -a > /firma/logs/system/system-info.txt
date >> /firma/logs/system/system-info.txt
df -h >> /firma/logs/system/system-info.txt
```

uname -a | sudo tee /firma/logs/system/system-info.txt
date | sudo tee -a /firma/logs/system/system-info.txt
df -h | sudo tee -a /firma/logs/system/system-info.txt


Linux BeagleBone 6.12.92-bone61 #1 PREEMPT Wed Jun  3 23:27:12 UTC 2026 armv7l GNU/Linux
Fr 12. Jun 15:21:29 CEST 2026
Dateisystem    Größe Benutzt Verf. Verw% Eingehängt auf
udev            213M       0  213M    0% /dev
tmpfs            49M    1,1M   48M    3% /run
/dev/mmcblk1p3  3,0G    1,7G  1,2G   60% /
tmpfs           242M       0  242M    0% /dev/shm
tmpfs           5,0M       0  5,0M    0% /run/lock
/dev/mmcblk1p1   36M    5,0K   36M    1% /boot/firmware
tmpfs            49M    4,0K   49M    1% /run/user/1000

**Die Firma bekommt echte Mitarbeiter!**

**Schritt 1: Abteilungsgruppen erstellen
**
```bash
# Gruppen für jede Abteilung
groupadd buchhaltung
groupadd it-abteilung
groupadd produktion
sudo groupadd management  Alle müssen auch hier mit sudo erstellt werden.

# Gruppen prüfen
tail /etc/group

cockpit-wsinstance:x:114:
admin:x:994:terence
tisdk:x:993:terence
weston-launch:x:992:terence
systemd-resolve:x:991:
terence:x:1000:
buchhaltung:x:1001:
it-abteilung:x:1002:
produktion:x:1003:
management:x:1004:
terence@BeagleBone:/firma$ 


**Schritt 2: Mitarbeiter anlegen**
```bash
# IT Mitarbeiter (darf sudo!)
useradd -c "IT Administrator" -g it-abte
ilung admin1
passwd admin1


# Buchhaltung
useradd -c "Buchhalterin" -g buchhaltung us
er-buch1
passwd user-buch1

# Produktion
useradd -c "Produktionsleiter" -g produktio
n user-prod1
passwd user-prod1


# Chef (in allen Gruppen!)
useradd -c "Geschaeftsfuehrer" chef
usermod -aG buchhaltung,it-abteilung,produk
tion,management chef
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
Wir haben Sudo nano benutzt. 


sudo tee /firma/it/scripts/user-erstellen.sh << 'EOF'
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
EOF


sudo chmod u+x /firma/it/scripts/user-erstellen.sh
sudo /firma/it/scripts/user-erstellen.sh