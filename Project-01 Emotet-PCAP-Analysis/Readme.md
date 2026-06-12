# Project 1 : Emotet PCAP Analysis
## A SOC alert triggered for suspicious HTTP traffic from an internal host (10.1.6.206). i analyzed the pcap to identify indicators of compromise.
## Tools Used
- Wireshark
- MITRE ATT&CK Framwork
  ## Investigation Steps
  ### 1. Intitial Traffic Filtering
  i applied the 'http.request' filter to identify all http requests from the infected host.
