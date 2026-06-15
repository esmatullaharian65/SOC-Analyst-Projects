
# Project 2: WannaCry Ransomware PCAP Analysis

## What I Did

I analyzed a PCAP of the WannaCry ransomware outbreak from May 2017. This is the one that spread like crazy across the world and hit the NHS hard. My goal was to see how it moves on the network and what signs an analyst should look for.

## Tools Used
- Wireshark
- MITRE ATT&CK (to map the techniques)

## What I Found

### The Infected Machine
The main bad host was `192.168.116.172`. It had way more traffic than most other machines on this small network.

### How It Spreads

I saw two main things in the traffic:

**1. SMB Traffic on Port 445**
The infected machine kept trying to connect to other computers using SMB. This is how Windows shares files normally, but here it was being abused. Some packets were marked as "Malformed" which is a red flag.

**2. Kerberos Authentication (Port 88)**
This was interesting. The malware didn't just use SMB — it also used Windows' own authentication system (Kerberos) to pretend to be legit. I followed the TCP stream and saw requests for `TESTDOMAIN.LOCAL` and `cifs` (file sharing service).

### File Enumeration

The scariest part was watching the malware list files on remote computers. It sent requests with `Pattern:*` which basically means "give me everything in this folder". Then it checked for `desktop.ini` and started poking around in `Administrator\Desktop\Share`.

That's the moment I knew it was looking for files to encrypt.

## Indicators of Compromise (IOCs)

| Type           | Value |
|----------------|-------|
| Infected Host  | 192.168.116.172 |
| Other Victims  | 192.168.116.138, 192.168.116.143, 192.168.116.149 |
| Domain         | TESTDOMAIN.LOCAL |
| Target Service | cifs (file sharing) |
| Suspicious Ports| 445 (SMB), 88 (Kerberos) |

## MITRE ATT&CK Mapping

| Technique                           | ID |    Where I Saw It |
|-------------------------------------|-------|-------------------------|
| Lateral Movement                    | T1021 | SMB connections to other hosts |
| Exploitation of Remote Services     | T1210 | Malformed SMB packets (EternalBlue) |
| Kerberos Authentication             | T1558 | TGS-REQ requests on port 88 |
| File and Directory Discovery        | T1083 | Pattern:* searches for files |

## What I'd Recommend

If I was responding to this as a SOC analyst:

1. **Block port 445 at the firewall** unless absolutely needed. WannaCry can't spread without it.

2. **Patch MS17-010 immediately**. This is the EternalBlue vulnerability. Check every Windows box.

3. **Isolate infected hosts first**. Don't wait. Pull `192.168.116.172` and any machine it talked to off the network.

4. **Look for Kerberos anomalies**. Watch for weird TGS-REQ requests from machines that don't normally do authentication.

5. **Check for `desktop.ini` or `Pattern:*` logs** in your EDR or file server logs. Those are telltale signs of scanning.

6. **Reset credentials** for any domain account the malware might have captured.

## Thoughts After This Analysis

WannaCry is loud once you know what to look for. The SMB scanning is hard to miss. But the Kerberos stuff is sneakier — it blends in with normal Windows traffic. If I was only watching for port 445, I might have missed part of the story.

The endpoints table helped a lot. Seeing four internal IPs with almost identical packet counts told me this wasn't just one infected machine trying to call home. It was spreading.

## Screenshots

### 1. SAM LOGON Requests (SMB)
![SAM LOGON](1-smb-sam-logon.png)

### 2. Traffic from Infected Host
![Infected Host Traffic](2-infected-host-traffic.png)

### 3. Kerberos on Port 88
![Kerberos Port 88](3-kerberos-port88.png)

### 4. All IP Addresses on the Network
![Endpoints IPv4](4-endpoints-ipv4.png)

### 5. SMB2 Connecting to Remote Shares
![SMB2 Tree Connect](5-smb2-tree-connect.png)

### 6. Malware Listing Files Remotely
![File Enumeration](6-smb2-file-enumeration.png)
