# Tag 1 – Capacity Planning & Monitoring

## Capacity Planning Tools

### sysstat installieren
sudo apt install sysstat
sudo systemctl enable --now sysstat
sudo nano /etc/default/sysstat
# ENABLED="true"
sudo systemctl restart sysstat

### sar – System Activity Reporter
sar -u 1 10    # CPU alle 1 Sek, 10 mal
sar -r 1 10    # Memory
sar -B 1 10    # Page/Swap
sar -n DEV 2 5 # Netzwerk

### Prozesse überwachen
top
w              # wer ist eingeloggt
uptime         # Load Average
pidof apache2  # PID eines Prozesses
top -p 915     # nur einen Prozess
top -Hp 1122   # alle Threads eines Prozesses

## Memory Messung
free -h
swapon -s
smem -k
smem -k -s rss
smem -k -t
pmap -x PID
vmstat

## Disk Activity
iostat
iostat -hxz 2 5
iotop
dstat
dstat -d    # Disk
dstat -y    # System
dstat -n    # Netzwerk
sar -d 1 5

## Netzwerk Activity
ip a s
ifconfig
ip -s link show eth0
ss -pant
ss -pantu
ss -at state ESTABLISHED
iftop -i eth0
nload eth0
bmon -p eth0
vnstat -i eth0
ethtool eth0