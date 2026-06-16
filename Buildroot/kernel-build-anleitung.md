# Linux Kernel Build Anleitung
## Optimierter Kernel für ThinkPad T510 (i5 M520)
### Erstellt aus praktischer Erfahrung – alle Fehler bereits gefunden!

---

## 1. Vorbereitung – System prüfen

### Speicherplatz prüfen (min. 20GB nötig)
```bash
df -h
```

### Hardware scannen – alle Geräte vorher anschließen!
- BBB per USB (Strom + UART)
- Externe Festplatte
- USB-Sticks
- WLAN verbunden
- Drucker aktiv

### Aktuelle Module speichern
```bash
lsmod > /tmp/meine_module.txt
```

---

## 2. Abhängigkeiten installieren
```bash
sudo apt install \
  build-essential \
  libncurses-dev \
  bison \
  flex \
  libssl-dev \
  libelf-dev \
  dwarves \
  bc \
  debhelper \
  libdw-dev \
  gawk
```

---

## 3. Kernel Quellcode holen

### Arbeitsverzeichnis erstellen
```bash
mkdir ~/kernel-build
cd ~/kernel-build
```

### Kernel herunterladen (LTS empfohlen!)
```bash
# Aktuelle LTS Version prüfen: https://kernel.org
wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.12.tar.xz
```

### Entpacken
```bash
tar xf linux-6.12.tar.xz
cd linux-6.12
```

---

## 4. Konfiguration – der richtige Weg

### Saubere Basis (NICHT Ubuntu Config kopieren!)
```bash
make defconfig
```

### Automatisch nur nötige Module aktivieren
```bash
make LSMOD=/tmp/meine_module.txt localmodconfig
```

### Bekannte Probleme sofort fixen (Ubuntu/Debian spezifisch)
```bash
scripts/config --disable SYSTEM_TRUSTED_KEYS
scripts/config --disable SYSTEM_REVOCATION_KEYS
scripts/config --disable DEBUG_INFO_BTF
```

### Kernel Namen setzen
```bash
scripts/config --set-str LOCALVERSION "-thinkpad"
```

### Neue Config Optionen mit Standardwerten übernehmen
```bash
make olddefconfig
```

---

## 5. Manuelle Optimierungen in menuconfig

```bash
make menuconfig
```

### Processor type and features:
- `[*] Build and optimize for local/native CPU` → **aktivieren**
- `[ ] Support for extended (non-PC) x86 platforms` → deaktivieren
- Unter `Supported processor vendors`:
  - AMD, Hygon, Centaur, Zhaoxin → alle deaktivieren
  - Nur Intel lassen

### Device Drivers – deaktivieren:
- `[ ] IEEE 1394 (FireWire) support`
- `[ ] Macintosh device drivers`
- `[ ] RapidIO support`
- `[ ] PC/104 support`
- `[ ] GNSS receiver support`
- `[ ] Sony MemoryStick card support`
- `[ ] InfiniBand support`
- `[ ] Amateur Radio support`
- `[ ] CAN bus subsystem support`
- `[ ] NFC subsystem support`
- `[ ] Greybus support`
- `[ ] Data acquisition support (comedi)`
- `[ ] Platform support for Chrome hardware`
- `[ ] Microsoft Surface Platform drivers`
- `[ ] SoundWire support`

### General setup:
- `()  Local version` → `-thinkpad` eintragen
- `<M> Kernel .config support` → aktivieren

---

## 6. Kernel bauen

```bash
make -j$(nproc) bzImage modules 2>&1 | tee ~/build.log
```

### Bei Fehlern im Log suchen:
```bash
grep "error:" ~/build.log | head -20
tail -50 ~/build.log
```

### Häufige Fehler und Fixes:

| Fehler | Fix |
|--------|-----|
| `canonical-certs.pem not found` | `scripts/config --disable SYSTEM_TRUSTED_KEYS` |
| `BTF: No data available` | `scripts/config --disable DEBUG_INFO_BTF` |
| `gawk: not found` | `sudo apt install gawk` |
| `debhelper-compat` Fehler | `sudo apt install debhelper` |
| `libdw-dev` fehlt | `sudo apt install libdw-dev` |

---

## 7. Kernel installieren

### Module installieren
```bash
sudo make modules_install
```

### Kernel installieren
```bash
sudo make install
```

### GRUB aktualisieren
```bash
sudo update-grub
```

### Prüfen ob Kernel im GRUB ist
```bash
grep -i "thinkpad" /boot/grub/grub.cfg
```

---

## 8. Neustarten und testen

```bash
sudo reboot
```

Im GRUB Menü den neuen Kernel `6.x.x-thinkpad` wählen.

### Nach dem Boot prüfen:
```bash
uname -a
# Sollte zeigen: Linux ... 6.x.x-thinkpad ...

lsmod | wc -l
# Weniger Module = besser optimiert

free -h
# Weniger RAM Verbrauch als vorher?
```

---

## 9. Nächste Optimierungsrunde

### Kernel Größe analysieren
```bash
ls -lh arch/x86/boot/bzImage
size vmlinux
```

### Geladene Module nach Boot prüfen
```bash
lsmod
```

### Module die nie geladen werden → in nächster Runde deaktivieren

---

## Lessons Learned (aus der Praxis)

1. **Immer `make defconfig` als Basis** – nicht Ubuntu Config kopieren
2. **`localmodconfig`** spart enorm viel Zeit
3. **Alle Tools vorher installieren** – spart Frust
4. **`tee` nutzen** – Build-Log immer speichern für Fehleranalyse
5. **LTS Kernel bevorzugen** – stabiler als neueste Version
6. **Alle Hardware vorher anschließen** bevor `lsmod` läuft
7. **Fehler am Ende** (beim Paketbauen) bedeuten nicht dass der Kernel kaputt ist
8. **Geduld** – erster Build dauert 1-2 Stunden, optimierter Build 20-30 Minuten

---

## Hardware Referenz ThinkPad T510

| Komponente | Treiber |
|------------|---------|
| CPU | Intel Core i5 M520 (Westmere) |
| Grafik | Intel HD Graphics (i915) |
| LAN | Intel 82577LM (e1000e) |
| WLAN | Intel Centrino Advanced-N 6200 (iwlwifi) |
| Audio | Intel HD Audio (snd_hda_intel) |
| SATA | Intel AHCI (ahci) |
| USB | Intel EHCI (ehci_intel) |
| SD-Karte | Ricoh MMC/SD (sdhci) |
| Bluetooth | Broadcom (btusb) |
| Webcam | Lenovo UVC (uvcvideo) |
| BBB UART | FTDI FT232 (ftdi_sio) |

---
*Erstellt: Juni 2026 | ThinkPad T510 | Debian/Ubuntu*
