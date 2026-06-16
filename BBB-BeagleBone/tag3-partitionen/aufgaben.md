## TAG 3 – Partitionen & Dateisysteme

### Lernziele:
- Partitionen erstellen mit parted
- Dateisysteme formatieren
- Mounten und fstab
- Swap verwalten



sudo parted /dev/mmcblk0 mkpart firmadaten ext4 1M 2G
sudo parted /dev/mmcblk0 mkpart firmaswap linux-swap 2G 2.5G


# Verfügbare Geräte prüfen
lsblk
parted /dev/mmcblk0 print free

# Neue Partition für Firmendaten erstellen
parted /dev/mmcblk1 mklabel gpt
parted /dev/mmcblk1 mkpart firmadaten ext4 1M 2G
parted /dev/mmcblk1 mkpart firmaswap linux-swap 
2G 2.5G
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

**Schritt 3: Persistent mounten**
```bash
# UUID ermitteln
blkid /dev/mmcblk1p1
blkid /dev/mmcblk1p2

# fstab editieren
nano /etc/fstab
UUID=DEINE-UUID /firmadaten ext4 defaults,nofail
 0 0
UUID=DEINE-UUID swap swap defaults 0 0

# Testen
systemctl daemon-reload
mount -a
lsblk
```