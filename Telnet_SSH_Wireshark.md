# Practical No. 1
## Aim: Examining Telnet & SSH in Wireshark (Packet Analyzer)

---

## PART A: TELNET ANALYSIS IN WIRESHARK (Windows)

### Step 1: Download Wireshark
- Go to https://www.wireshark.org and download & install Wireshark for Windows.

### Step 2: Enable Telnet Client on Windows
1. Open **Control Panel** → **Programs** → **Turn Windows features on or off**
2. Scroll down and check **Telnet Client**
3. Click **OK** and wait for installation to complete.

### Step 3: Connect to Telnet Server via CMD
Open Command Prompt (cmd) and type:
```
telnet telehack.com
```
- You will be connected to TELEHACK port 160.
- You can interact with the ELIZA bot by typing `.ELIZA` and then entering your name.

### Step 4: Capture and Analyze in Wireshark
1. Before or while running Telnet, open **Wireshark**.
2. Select your active network interface and start capturing.
3. In the Wireshark filter bar, type:
```
telnet
```
4. Press **Enter** — you will see all Telnet packets.
5. Each packet shows: Source IP, Destination IP, Protocol (TELNET), Length, and Info (e.g., "1 byte data", "2 bytes data").
6. You can right-click any packet → **Follow** → **TCP Stream** to see the entire conversation in plain text (since Telnet is unencrypted).

**Key Observation:** Telnet sends data in **plaintext** — usernames, passwords, and commands are all visible in Wireshark. This is why Telnet is considered insecure.

---

## PART B: SSH/FTP ANALYSIS IN WIRESHARK (Kali Linux)

### Step 1: Connect to FTP Server in Kali
Open terminal in Kali Linux and type:
```bash
ftp test.rebex.net
```
- When prompted for Name: type `demo`
- When prompted for Password: type `password`
- This connects to a read-only FTP test server.

### Step 2: Open Wireshark Simultaneously
- Open Wireshark in Kali Linux: go to **File** → search **Wireshark** → press Enter.
- Select `eth0` interface and start capturing.

### Step 3: Filter by SSH Port
After capturing for a while, stop the capture and type in the filter bar:
```
tcp.port==22
```
- This filters all SSH traffic on port 22.
- If SSH is not active, filter by `tcp` to see FTP packets.

### Step 4: Follow TCP Stream
1. Right-click on any TCP/FTP packet in the list.
2. Go to **Follow** → **TCP Stream**.
3. A new window appears showing the full conversation between client and server.
4. You can see the FTP login sequence: USER demo, PASS password, server responses, SYST, FEAT commands, etc.

**Key Observation:** FTP (like Telnet) sends data in plaintext. SSH, on the other hand, encrypts all data — the TCP stream for SSH shows only encrypted/binary content, making it unreadable.

---

## Summary of Key Points
- **Telnet** → Plaintext protocol, all data visible in Wireshark.
- **FTP** → Plaintext protocol, credentials visible in Wireshark.
- **SSH** → Encrypted protocol, data is not readable in Wireshark.
- Wireshark filter `telnet` shows Telnet packets; `tcp.port==22` shows SSH packets.
- Use **Follow TCP Stream** to read the full conversation of any TCP session.
