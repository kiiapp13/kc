
## Step 1: Write the Port Scanner Code in Python

Create a file `port_scanner.py` with the following code:

```python
import socket

def scan_ports(target, ports):
    print(f"Scanning {target}...\n")
    
    for port in ports:
        try:
            # Create a TCP socket
            sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            sock.settimeout(1)    # 1 second timeout per port
            
            # Try to connect to the target on this port
            result = sock.connect_ex((target, port))
            
            if result == 0:
                print(f"[OPEN] Port {port}")
            
            sock.close()
        
        except Exception as e:
            print(f"Error scanning port {port}: {e}")

if __name__ == "__main__":
    target_ip = input("Enter target IP (e.g., 127.0.0.1): ")
    
    # Common ports to scan
    common_ports = [21, 22, 23, 25, 53, 80, 110, 139, 143, 443, 445, 3389]
    
    scan_ports(target_ip, common_ports)
```

---

## Step 2: Set Up the Target Machine (Kali Linux)

Open **Kali Linux** in VirtualBox/VMware.

### Configure Network (Bridge Adapter):
1. Go to VirtualBox **Settings** → **Network**.
2. Change **Adapter 1** from NAT to **Bridged Adapter**.
3. This puts Kali on the same network as your host machine.

### Find Kali's IP Address:
```bash
ifconfig
```

Expected output:
```
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
      inet 192.168.21.56  netmask 255.255.252.0  broadcast 192.168.23.255
      inet6 fe80::d602:b051:376a:3023  preflen 64  scopeid 0x20<link>
      ether 08:00:27:6e:13:6e  txqueuelen 1000  (Ethernet)
      RX packets 2173  bytes 157132 (153.4 KiB)
      TX packets 44  bytes 10979 (10.7 KiB)
```

Your Kali IP is `192.168.21.56` (the value after `inet`).

---

## Step 3: Start SSH Service on Kali (to have an open port to scan)

```bash
systemctl start ssh
```

### Check SSH status:
```bash
systemctl status ssh
```

Expected output:
```
● ssh.service - OpenBSD Secure Shell server
   Loaded: loaded (/usr/lib/systemd/system/ssh.service; enabled; preset: disabled)
   Active: active (running) since Sun 2026-03-22 01:53:21 EDT; 3min 39s ago
   Main PID: 798 (sshd)
```
Also shows:
```
Mar 22 01:53:21 kali sshd[798]: Server listening on 0.0.0.0 port 22.
Mar 22 01:53:21 kali sshd[798]: Server listening on :: port 22.
```

This confirms port 22 is now open.

---

## Step 4: Run the Port Scanner

On your **Windows machine** (or wherever Python is installed), run:
```bash
python port_scanner.py
```

When prompted:
```
Enter target IP (e.g., 127.0.0.1): 192.168.21.56
```

Enter the Kali Linux IP address found in Step 2.

---

## Step 5: Observe the Output

Expected output:
```
Scanning 192.168.21.56...

[OPEN] Port 80
```

(Port 80 is open because Apache web server is running)
(Port 22 may appear if SSH is running and firewall allows it)

---

## Step 6: Stop SSH and Verify Port Closes

On Kali Linux, stop the SSH service:
```bash
systemctl stop ssh
```

Check status (should show inactive/dead):
```bash
systemctl status ssh
```
```
● ssh.service - OpenBSD Secure Shell server
   Active: inactive (dead) since Sun 2026-03-22 01:57:40 EDT; 20s ago
```
```
Mar 22 01:57:40 kali sshd[798]: Server listening on 0.0.0.0 port 22.
Mar 22 01:57:40 kali systemd[1]: Stopping ssh.service - OpenBSD Secure Shell server...
Mar 22 01:57:40 kali systemd[1]: Stopped ssh.service - OpenBSD Secure Shell server.
```

Now run the port scanner again — Port 22 should no longer appear as open.

---

## Enhanced Port Scanner with Service Names

```python
import socket

# Port to service name mapping
SERVICE_MAP = {
    21: "FTP",
    22: "SSH",
    23: "Telnet",
    25: "SMTP",
    53: "DNS",
    80: "HTTP",
    110: "POP3",
    139: "NetBIOS",
    143: "IMAP",
    443: "HTTPS",
    445: "SMB",
    3389: "RDP"
}

def scan_ports(target, ports):
    print(f"\nScanning target: {target}")
    print("-" * 40)
    open_ports = []
    
    for port in ports:
        try:
            sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            sock.settimeout(1)
            result = sock.connect_ex((target, port))
            
            if result == 0:
                service = SERVICE_MAP.get(port, "Unknown")
                print(f"[OPEN]   Port {port:5d}  →  {service}")
                open_ports.append(port)
            else:
                service = SERVICE_MAP.get(port, "Unknown")
                print(f"[CLOSED] Port {port:5d}  →  {service}")
            
            sock.close()
        
        except socket.error as e:
            print(f"[ERROR]  Port {port}: {e}")
    
    print("-" * 40)
    print(f"Scan complete. {len(open_ports)} open port(s) found: {open_ports}")

if __name__ == "__main__":
    target_ip = input("Enter target IP: ").strip()
    common_ports = [21, 22, 23, 25, 53, 80, 110, 139, 143, 443, 445, 3389]
    scan_ports(target_ip, common_ports)
```


