<h1>Tools Used</h1>
Wireshark
<h1>Scenario</h1>
The SOC team has identified suspicious activity on a web server within the company's intranet. To better understand the situation, they have captured network traffic for analysis. The PCAP file may contain evidence of malicious activities that led to the compromise of the Apache Tomcat web server. Your task is to analyze the PCAP file to understand the scope of the attack.
<h1>Questions</h1>
<h3>Q1. Given the suspicious activity detected on the web server, the PCAP file reveals a series of requests across various ports, indicating potential scanning behavior. Can you identify the source IP address responsible for initiating these requests on our server</h3>
Firstly examining the network traffic, communications between 3 IP addresses with identical network portions can be seen communicating with eachother, 10.0.0.115 finishes communications with 10.0.0.105 then establishes one with 10.0.0.112, this is a client and a server, communicating on identical ports, so they are ruled out.
<br>
<img width="1176" height="418" alt="image" src="https://github.com/user-attachments/assets/4ae5757a-2db4-4d55-93c8-829b5bb53a11" />
<br>
The attacker is made clear when the foreign IP address 14.0.0.120 starts sending multiple SYN packets to connect on port 51985 to 10.0.0.112 on ports 256,443,199,113 and 3306, followed by multiple rejections by RST ACK packets, suggesting that they are scanning for open ports on the server.
<br>
<img width="1181" height="340" alt="image" src="https://github.com/user-attachments/assets/993435b9-35be-4f1e-808a-b2273eb19b51" />
<h4>Answer: 14.0.0.120</h4>

