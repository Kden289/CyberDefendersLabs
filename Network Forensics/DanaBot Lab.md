<h1>Tools</h1>
Wireshark
VirusTotal
<h1>Scenario</h1>
The SOC team has detected suspicious activity in the network traffic, revealing that a machine has been compromised. Sensitive company information has been stolen. Your task is to use Network Capture (PCAP) files and Threat Intelligence to investigate the incident and determine how the breach occurred.
<h1>Questions</h1>
<h3>Q1.Which IP address was used by the attacker during the initial access?</h3>
Initially, the IP address 10.2.14.101 tries to access the website portfolo.serveirc.com, this is most likely a typo and indicates that the website they are accessing may not be the correct one. The DNS server 10.2.14.1 responds and returns the website for them to access.
<img width="1920" height="325" alt="image" src="https://github.com/user-attachments/assets/fa82b836-c476-4495-b944-c7115502eb7d" />
After the DNS is resolved, a connection is established between 10.2.14.101 and 62.173.142.148 with a three-way handshake (SYN→SYN-ACK→ACK), and data is being sent between the IP addresses.
But the IP address 62.173.142.148 is highly suspicious, searching it on VirusTotal reveals that it has been flagged a few times as malicious, confirming it to be the attacker.
<img width="1702" height="212" alt="image" src="https://github.com/user-attachments/assets/5281e79a-6615-4bd3-a8ff-9433f2be21c6" />
