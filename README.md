# Network-Scanner-Logger-
Useing a Raspbarry pi5 as a headless command line tool to scan home network,log connected devices, and monitor changes over time. 


# What You'll Learn
-Linux terminal basics

-Networking tools (nmap, arp, ip, netdiscover)

-Cron jobs for automation

-Logging + text manipulation

# What You'll Need 
- Raspberry Pi ( 3,4, or 5)
- MicroSd with Raspabbry Pi OS lite
- SSH enabled (or keyboard/HDMI to start)
- Wifi or Ethernet


# Project Flow 

# Step 1: Flash Raspberry Pi OS Lite

Use Raspberry Pi Imager:
- OS: Raspberry Pi OS Lite (64-bit)
- Enable SSH in advanced settings (gear icon)
- Set hostname and Wi-Fi if desired
- Boot Pi → SSH in from your main computer

---

## Step 2: Connect to Pi & Update

From the main computer:

```bash
ssh pi@<your-pi-ip>
````

Update system:

```bash
sudo apt update && sudo apt full-upgrade -y
```

---

## Step 3: Install Required Tools

```bash
sudo apt install nmap -y
sudo apt install arp-scan curl htop -y
```

---

## Step 4: Create Logging Directory

```bash
mkdir ~/net-monitor-logs
```

---

## Step 5: Create Main Scan Script

```bash
nano ~/net-monitor.sh
```

Paste this code:

```bash
#!/bin/bash

LOG_DIR=~/net-monitor-logs
DATE=$(date "+%Y-%m-%d_%H-%M-%S")
LOG_FILE="$LOG_DIR/scan_$DATE.txt"
NETWORK="192.168.1.0/24"  # Change to your local network range

mkdir -p "$LOG_DIR"
echo "Scanning network at $DATE..." >> "$LOG_FILE"
nmap -sn "$NETWORK" | grep "Nmap scan report for" >> "$LOG_FILE"
echo "Done. Log saved to $LOG_FILE"
```

Save:

> Press `Ctrl + X`, then `Y`, then `Enter`

Make executable:

```bash
chmod +x ~/net-monitor.sh
```

---

## Step 6: Test the Script

Run it:

```bash
./net-monitor.sh
```

Check the output:

```bash
cat ~/net-monitor-logs/scan_*.txt
```

---

## Step 7: Schedule with Cron

Edit the crontab:

```bash
crontab -e
```

Add this line to schedule the script daily at 7:00 PM:

```bash
0 19 * * * /home/pi/net-monitor.sh
```

---

## Step 8: (Optional) Add Log Cleanup

Create the cleanup script:

```bash
mkdir ~/network-monitor-pi/scripts
nano ~/network-monitor-pi/scripts/cleanup-logs.sh
```

Paste this:

```bash
#!/bin/bash
find ~/net-monitor-logs -type f -mtime +7 -print -delete
```

Make it executable:

```bash
chmod +x ~/network-monitor-pi/scripts/cleanup-logs.sh
```

Schedule it to run every Sunday at 8:00 PM:

```bash
crontab -e
```

Add:

```bash
0 20 * * 0 /home/pi/network-monitor-pi/scripts/cleanup-logs.sh
```


