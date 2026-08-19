<h1>Tools Used</h1>
Wireshark
<br><br>
VirusTotal
<h1>Scenario</h1>
The SOC team has identified suspicious activity on a web server within the company's intranet. To better understand the situation, they have captured network traffic for analysis. The PCAP file may contain evidence of malicious activities that led to the compromise of the Apache Tomcat web server. Your task is to analyze the PCAP file to understand the scope of the attack.
<h1>Questions</h1>
<h3>Q1. Given the suspicious activity detected on the web server, the PCAP file reveals a series of requests across various ports, indicating potential scanning behavior. Can you identify the source IP address responsible for initiating these requests on our server</h3>
Firstly examining the network traffic, communications between 3 IP addresses with identical network portions can be seen communicating with eachother, 10.0.0.115 finishes communications with 10.0.0.105 then establishes one with 10.0.0.112, this is a client and a server, communicating on identical ports, so they are ruled out.
<br><br>
<img width="1176" height="418" alt="image" src="https://github.com/user-attachments/assets/4ae5757a-2db4-4d55-93c8-829b5bb53a11" />
<br><br>
The attacker is made clear when the foreign IP address 14.0.0.120 starts sending multiple SYN packets to connect on port 51985 to 10.0.0.112 on ports 256,443,199,113 and 3306, followed by multiple rejections by RST ACK packets, suggesting that they are scanning for open ports on the server.
<br><br>
<img width="1181" height="340" alt="image" src="https://github.com/user-attachments/assets/993435b9-35be-4f1e-808a-b2273eb19b51" />
<h4>Answer: 14.0.0.120</h4>
<h3>Q2.Based on the identified IP address associated with the attacker, can you identify the country from which the attacker's activities originated?</h3>
Putting the IP address through to VirusTotal reveals it to be from China.
<br><br>
<img width="1692" height="217" alt="image" src="https://github.com/user-attachments/assets/21d975a9-ed5c-48e2-b35e-c64fa64ccfc1" />
<h4>Answer: China</h4>
<h3>Q3.From the PCAP file, multiple open ports were detected as a result of the attacker's active scan. Which of these ports provides access to the web server admin panel?</h3>
Because the admin panel is a web server, filtering for packets that use HTTP packets will reveal that the port that provides access to the admin panel is 8080, 14.0.0.120 is seen requesting several files from the server aswell, with what seems to be lots of confidential information.
<br><br>
<img width="1172" height="356" alt="image" src="https://github.com/user-attachments/assets/0e7d0685-62b3-4fcf-8500-4ff1a598bd0b" />
<h3>Q4.Following the discovery of open ports on our server, it appears that the attacker attempted to enumerate and uncover directories and files on our web server. Which tools can you identify from the analysis that assisted the attacker in this enumeration process?</h3>
There were failed attempts from the attacker to take some files from the admin panel, the packets from the server that returned an error from the request can help in finding out the tool that 14.0.0.120 used. Using the filter http.request.uri and looking into the network traffic reveals Error 404 not found packets sent from the server.
<br><br>
<img width="1175" height="430" alt="image" src="https://github.com/user-attachments/assets/afa8ac35-a4a1-4f33-a832-afe3d786f37e" />
<br><br>
Following any of the Error 404 packets HTTP traces back to failed requests made from the attacker, when viewing the packets HTTP info, the user-agent making the request is the application named gobuster.
<br><br>
<img width="1180" height="636" alt="image" src="https://github.com/user-attachments/assets/fce15533-2ba7-40bd-af72-1e48b2cdc04c" />
<h4>Answer: gobuster</h4>
<h3>Q5. After enumerating directories on our web server, the attacker made numerous requests to identify administrative interfaces. Which directory related to the admin panel did the attacker uncover? (Provide the path including the leading slash, e.g. /path)</h3>
To filter the attackers request of administrative files, the filter http.request.uri and ip.src==14.0.0.120 filters all file requests from the attacker, helping to narrow down what directory they targetted.
<br><br>
Sifting through the filtered network, there is a noticeable group of packets in which the attacker targets the /manager directory for files, fitting the criteria for numerous attempts made, aswell as it being a folder directly related to the admin panel.
<br><br>
<img width="1171" height="410" alt="image" src="https://github.com/user-attachments/assets/a1e24618-28d0-4d38-8120-e36082a73308" />
<h4>Answer: /manager</h4>

