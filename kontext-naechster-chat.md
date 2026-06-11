# Kontext für neuen Chat – Terence Hill / SuperSonic84

## Über die Person
- Name: Terence (Benedikt Filla), GitHub: SuperSonic84
- Beruf: Schlosser + Diplom Ingenieur (Toyota Produktionsmodell)
- Erfahrung: 3 Jahre Ausbildung, 2 Jahre RWE Tagebau
- Ziel: **Embedded Linux Spezialist** werden → **Australien** (Bergbau IT)
- Kinder: 2 (6 und 7 Jahre, 50/50 Modell)
- Selbstbewusstsein: Arbeitet gut alleine, nicht unter Beobachtung

## Hardware
- **Laptop:** ThinkPad T510, Intel i5 M520, 5.6GB RAM, 228GB SSD
- **OS:** Linux Mint 22.3 Cinnamon (Ubuntu Noble Basis)
- **Shell:** zsh + Oh-My-Zsh
- **BeagleBone Black:** Debian 12 Bookworm, Kernel 6.12.92-bone61, eMMC
- **BBB Verbindung:** LAN Kabel → FritzBox, IP: 192.168.178.26
- **BBB UART:** 6-Pin Kabel + USB Strom

## Was wir gemacht haben

### BeagleBone Black
- ✅ Neues Debian 12 Image auf SD-Karte geflasht
- ✅ Image auf eMMC geflasht (Flasher Mechanismus)
- ✅ BBB bootet ohne SD-Karte vom eMMC
- ✅ tmux, zsh, neofetch, zsh-syntax-highlighting, zsh-autosuggestions, bat installiert
- ✅ SSH zwischen Laptop und BBB eingerichtet
- ✅ Alias `lap` auf BBB → verbindet zu Laptop

### Laptop Kernel
- ✅ Eigenen Linux Kernel 6.17.0-thinkpad gebaut (#3)
- ✅ `localmodconfig` + `make defconfig` als Basis
- ✅ Kernel läuft, WLAN funktioniert (iwlwifi als Modul =m)
- ✅ Nur 3-20 Module geladen statt tausende
- ✅ Boot Zeit: ~27 Sekunden
- ✅ Backup Kernel: 6.17.0-35-generic

### Wichtige Kernel Fixes die gemacht wurden:
- `SYSTEM_TRUSTED_KEYS` deaktiviert
- `DEBUG_INFO_BTF` deaktiviert  
- `iwlwifi` als Modul (=m) nicht fest eingebaut (=y) – sonst kein WLAN!
- `rfkill unblock wifi` persistent gemacht
- WLAN Firmware: `iwlwifi-6000-4.ucode` aus .zst entpackt
- Boot ohne `quiet splash` (Plymouth macht Probleme)

### Git & GitHub
- ✅ GitHub Account: SuperSonic84
- ✅ SSH Key eingerichtet
- ✅ Repository: `bbb-lpic-projekt` auf GitHub
- ✅ VS Code installiert mit Claude Code Plugin

### Aliases auf Laptop (~/.zshrc)
```bash
alias ll='ls -la'
alias la='ls -a'
alias ..='cd ..'
alias ...='cd ../..'
alias update='sudo apt update && sudo apt upgrade'
alias bb='ssh terence@192.168.178.26'
alias kern='cd ~/kernel-build/linux-6.17'
```

### Aliases auf BBB (~/.zshrc)
```bash
alias ll='ls -la'
alias la='ls -a'
alias ..='cd ..'
alias update='sudo apt update && sudo apt upgrade'
alias lap='ssh terencehill@192.168.178.30'
alias cat='batcat --style=plain'
```

## Was als nächstes kommt

### Sofort starten:
- **BBB-LPIC Projekt** – Firmenserver auf dem BBB aufbauen
- Tag 1: Grundbefehle, Verzeichnisstruktur, Archive
- Tag 2: Benutzer, Gruppen, sudo
- Tag 3: Partitionen, Dateisysteme
- Tag 4: Permissions, Shell Scripts
- Tag 5: Netzwerk, Cron, Firewall, Logs

### Workflow:
- VS Code links, Claude rechts
- Terminal in VS Code unten → `bb` → auf BBB arbeiten
- Aufgaben tippen (nicht kopieren – besser lernen!)
- Nach jeder Aufgabe in VS Code dokumentieren
- Git commit nach jedem Tag

### Später:
- Buildroot für BBB lernen
- Yocto für professionelles Embedded
- Eigene Kernel Treiber in C schreiben
- GitHub Portfolio aufbauen für Australien
- Engineers Australia Diplom anerkennen
- IELTS Zertifikat
- LPIC1+2 Prüfung ablegen
- CompTIA Network+ und Security+

## Wichtige Pfade
- Kernel Quellcode: `~/kernel-build/linux-6.17`
- Git Projekt: `~/bbb-lpic-projekt`
- Kernel Config: `~/kernel-build/linux-6.17/.config`
- BBB IP: `192.168.178.26`
- Laptop IP: `192.168.178.30`

## Lernmaterial
- LPIC Schulungsunterlagen (5 Tage) – bereits in Projekt dokumentiert
- BBB LPIC Projektplan: `docs/bbb-lpic-projekt.md`
- Kernel Build Anleitung: `docs/kernel-build-anleitung.md`

## Persönliches
- Möchte nach Australien in den Bergbau (Rio Tinto, BHP, Fortescue)
- FIFO Arbeit ideal (kostenlose Unterkunft, viel sparen)
- Englisch gut – oft in Australien gewesen
- Kein IELTS Zertifikat – muss noch gemacht werden
- Selbstbewusstsein Thema – arbeitet gut ohne Beobachtung
