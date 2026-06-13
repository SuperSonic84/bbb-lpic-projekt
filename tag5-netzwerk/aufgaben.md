## TAG 5 – Netzwerk, Cron, Logs & Firewall

### Lernziele:
- Netzwerk konfigurieren
- Cronjobs einrichten
- Logs mit journalctl analysieren
- Firewall konfigurieren

### Projekt-Aufgabe auf dem BBB:

**Server wird abgesichert und automatisiert!**

**Schritt 1: Cronjobs einrichten**
crontab -e

# Tägliches Backup um 2 Uhr nachts
0 2 * * * /firma/it/scripts/backup.sh >> /firma/
logs/system/backup.log 2>&1

# Stündlich System-Info loggen
0 * * * * echo "$(date): $(uptime)" >> /firma/lo
gs/system/uptime.log

# Alte Logs wöchentlich löschen (älter als 30 Ta
ge)
0 3 * * 0 find /firma/logs -mtime +30 -delete

# Cronjobs prüfen
crontab -l
:
```bash
crontab -e

# Tägliches Backup um 2 Uhr nachts
0 2 * * * /firma/it/scripts/backup.sh >> /firma/
logs/system/backup.log 2>&1

# Stündlich System-Info loggen
0 * * * * echo "$(date): $(uptime)" >> /firma/lo
gs/system/uptime.log

# Alte Logs wöchentlich löschen (älter als 30 Ta
ge)
0 3 * * 0 find /firma/logs -mtime +30 -delete

# Cronjobs prüfen
crontab -l

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


journalctl -p err
journalctl --since "1 hour ago"
journalctl -u apache2




**Schritt 3: Firewall einrichten**
```bash
# ufw installieren und aktivieren
apt install ufw -y
ufw enable

# Nur nötige Ports öffnen
ufw allow ssh          # SSH Zugang
ufw allow 80/tcp       # Webserver
ufw allow from 192.168.7.0/24  # Nur vom Laptop        Sudo nicht vergessen 

# Status prüfen
ufw status numbered
```

sudo nano /firma/it/scripts/monitoring.sh


**Schritt 4: Monitoring Script**
```bash
cat > /firma/it/scripts/monitoring.sh
#!/bin/bash
LOG=/firma/logs/system/monitoring.log




#!/bin/bash
LOG=/firma/logs/system/monitoring.log
echo "=== $(date) ===" >> $LOG
echo "CPU Last: $(top -bn1 | grep 'Cpu(s)' | awk '{print $2}')%" >> $LOG
echo "RAM: $(free -h | grep Mem | awk '{print $3"/"$2}')" >> $LOG
echo "Disk: $(df -h / | tail -1 | awk '{print $5}')" >> $LOG
echo "Temperatur: $(cat /sys/class/thermal/thermal_zone0/temp)" >> $LOG
echo "" >> $LOG

sudo chmod u+x /firma/it/scripts/monitoring.sh


# In Cronjob eintragen
crontab -e
*/5 * * * * /firma/it/scripts/monitoring.
sh
```
