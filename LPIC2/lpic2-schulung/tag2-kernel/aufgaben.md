# Tag 2 – Kernel Management

## Kernel Parameter (sysctl)
sudo sysctl --all
sudo sysctl -w vm.swappiness=40
sudo sysctl -w net.ipv4.ip_forward=1
sudo sysctl --all | grep vm.swappiness
sudo sysctl --all | grep net.ipv4.ip_forward

### Persistent machen
sudo nano /etc/sysctl.conf
# vm.swappiness=40
# net.ipv4.ip_forward=1
sudo sysctl --system

## Kernel Module
lsmod                    # alle geladenen Module
modinfo mptcp_diag       # Info über Modul

### Module laden/entladen
lsmod | grep -i mptcp_diag
sudo rmmod mptcp_diag
sudo insmod /lib/modules/6.8.0-57-generic/kernel/net/mptcp/mptcp_diag.ko
sudo modprobe -r mptcp_diag   # entladen mit Abhängigkeiten
sudo modprobe -a mptcp_diag   # laden mit Abhängigkeiten

## /proc Filesystem
cat /proc/cpuinfo      # CPU Info
cat /proc/meminfo      # RAM Info
cat /proc/interrupts   # Hardware Interrupts
cat /proc/loadavg      # Load Average
cat /proc/version      # Kernel Version
ls /proc/sys/kernel    # Kernel Parameter

## Hardware erkennen
lsusb
lspci
lsdev

## Logs
dmesg
cat /var/log/dmesg