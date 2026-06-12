#########################################################
## Pusch befehl
cd ~/bbb-lpic-projekt
git add .
git commit -m "Tag 1: Firmenstruktur und Backup Script erstellt"
git push 






**Schritt 1: Projektstruktur anlegen**
```bash
# Verbinde dich per SSH mit dem BBB
ssh 

# Firmen-Verzeichnisstruktur erstellen
mkdir -p /firma/{buchhaltung,it,produktion,logs,backup}
mkdir -p /firma/it/{scripts,config,docs}
mkdir -p /firma/logs/{system,web,mail}

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

#sudo tee /firma/it/docs/handbuch.txt << 'EOF'
BBB Firmenserver - IT Handbuch
Erstellt von: terence
Datum: $(date)
EOF

# Datei anzeigen
cat /firma/it/docs/handbuch.txt

# Daten anhängen
echo "Version: 1.0" >> /firma/it/docs/handbuch.txt

**Schritt 3: Suchen und Finden**
```bash
# Alle .txt Dateien finden
find /firma -name "*.txt"

# Alle Dateien größer als 1KB
find /firma -size +1k

# Logs älter als 7 Tage (später für Cronjob!)
find /firma/logs -mtime +7
```
sudo tee /firma/it/scripts/backup.sh << 'EOF'
#!/bin/bash
DATUM=$(date +%Y-%m-%d)
BACKUP_DIR=/firma/backup
tar -czf $BACKUP_DIR/firma-backup-$DATUM.tar.gz /firma/buchhaltung /firma/produktion
echo "Backup erstellt: firma-backup-$DATUM.tar.gz"
EOF

## Buckup erstellt und Skript geschreiben 
## Datei muss Rechte verändert bekommen    sudo chmod u+x /firma/it/scripts/backup.sh
## Datei wird mit root rechten gestartet   sudo /firma/it/scripts/backup.sh

# Backup prüfen
ls -lh /firma/backup/
tar -tf /firma/backup/firma-backup-*.tar.gz