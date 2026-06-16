## TAG 4 – Permissions, Scripts & Services

### Lernziele:
- Dateiberechtigungen verstehen
- SUID, SGID, Sticky Bit
- Shell Scripts schreiben
- Services verwalten

### Projekt-Aufgabe auf dem BBB:
sudo chown root:buchhaltung /firma/buchhaltung
sudo chown root:it-abteilung /firma/it
sudo chown root:produktion /firma/produktion


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

**Schritt 2: Admin Tool Script (aus LPIC Schulun
g!)**
```bash
cat > /firma/it/scripts/tool.sh
#!/bin/bash
while true
do
    e

sudo tee /firma/it/scripts/tool.sh << 'EOF'

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


chmod u+x /firma/it/scripts/tool.sh
cp /firma/it/scripts/tool.sh /usr/local/bin/firm
antool
```


**Schritt 3: Webserver als Firmen-Intranet**


```bash
# Apache installieren
apt install apache2 -y
systemctl enable apache2
systemctl start apache2





cat > /var/www/html/index.html
<!DOCTYPE html>
<html>
<head><title>BBB Firmenserver Intranet</title></
head>
<body>
<h1>Willkommen im Firmennetzwerk!</h1>
<p>Server: BeagleBone Black</p>
<p>Datum: $(date)</p>
</body>
</html>
^C

# Status prüfen
systemctl status apache2