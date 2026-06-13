# Project 1: Emotet PCAP Analysis
## 📌 Overview
A SOC alert was triggered for suspicious HTTP traffic originating from an internal host (10.1.6.206). This project documents the analysis of the associated packet capture (Example-1-2021-01-06-Emotet-infection.pcap) to identify Indicators of Compromise (IOCs) and understand the malware’s behavior.

## 🛠 Tools Used
**Wireshark** – for packet inspection and stream reconstruction
**MITRE ATT&CK Framework** – for mapping observed behaviors to adversary techniques

## 🔎 Investigation Steps
### 1. Initial Traffic Filtering
Applied the http.request filter to isolate all HTTP requests from the infected host. This revealed multiple suspicious outbound connections.

### 2. C2 Communication Discovery
Observed repeated HTTP POST requests to external IP addresses, consistent with Command-and-Control (C2) traffic. Payloads were transmitted using multipart/form-data, suggesting data exfiltration.

### 3. TCP Stream Analysis
Following the TCP streams revealed:
Request: Malicious file upload disguised as multipart/form-data

### 4. Endpoint Statistics
Endpoint analysis showed heavy communication between:
- Internal host: 10.1.6.206
- External IP: 5.2.136.90

This persistent traffic pattern is a strong IOC for Emotet infection.

### 5. MITRE ATT&CK Mapping
* Exfiltration Over Unencrypted Channel (T1048) – data sent via HTTP POST
* Command and Control over HTTP (T1071.001) – repeated beaconing to external IP
* Obfuscated Files or Information (T1027) – garbled/encrypted payloads in responses
  
##  📂 Indicators of Compromise (IOCs)
**Internal Host: 10.1.6.206**
**C2 Server: 5.2.136.90**
**Suspicious HTTP Paths: /7ub0ej2avlvnuvnyyo/szcmZnK/fzb067wy/**
**User-Agent String: Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 10.0; WOW64; Trident/7.0; .NET4.0C; .NET4.0E)**

## Conclusion
The PCAP analysis confirmed an Emotet infection on the internal host. Evidence includes suspicious HTTP requests, C2 communication, and exfiltration attempts. These findings provide actionable IOCs for detection and prevention in future incidents.

Response: Obfuscated/garbled payloads returned from the C2 server, indicating layered evasion and possible malware updates

