<img width="1172" height="427" alt="image" src="https://github.com/user-attachments/assets/2d64d380-7efe-4568-9f61-778ceb036260" /><h1>Tools Used</h1>
Wireshark
<h1>Scenario</h1>
You are a cybersecurity analyst working in the Security Operations Center (SOC) of BookWorld, an expansive online bookstore renowned for its vast selection of literature. BookWorld prides itself on providing a seamless and secure shopping experience for book enthusiasts around the globe. Recently, you've been tasked with reinforcing the company's cybersecurity posture, monitoring network traffic, and ensuring that the digital environment remains safe from threats.
Late one evening, an automated alert is triggered by an unusual spike in database queries and server resource usage, indicating potential malicious activity. This anomaly raises concerns about the integrity of BookWorld's customer data and internal systems, prompting an immediate and thorough investigation.
As the lead analyst in this case, you are required to analyze the network traffic to uncover the nature of the suspicious activity. Your objectives include identifying the attack vector, assessing the scope of any potential data breach, and determining if the attacker gained further access to BookWorld's internal systems.
<h1>Questions</h1>
<h3>Q1.By knowing the attacker's IP, we can analyze all logs and actions related to that IP and determine the extent of the attack, the duration of the attack, and the techniques used. Can you provide the attacker's IP?</h3>
Viewing the statistics of the network traffic from Statistics→IPv4 Statistics→All Addresses, the two most notable IP addresses are 73.124.22.98 and 111.224.250.131, they have the most
packets sent out of every IP address with very similar amounts, suggesting a connection with eachother and reflecting the unusual spike in traffic.
<br><br>
<img width="911" height="547" alt="image" src="https://github.com/user-attachments/assets/d9e1a3c0-d934-4047-a907-a2db06620849" />
Further investigating these IPs by filtering in ip.addr==111.224.250.131 and ip.addr==73.124.22.98 shows several sensitive file requests being made from 111.224.250.131 to the server.
<h4>Answer: 111.224.250.131</h4>
<h3>Q2. If the geographical origin of an IP address is known to be from a region that has no business or expected traffic with our network, this can be an indicator of a targeted attack. Can you determine the origin city of the attacker?</h3>
Putting the IP address through to https://whatismyipaddress.com reveals the origin city of the attacker to be from Shijiazhuang.
<img width="1026" height="786" alt="image" src="https://github.com/user-attachments/assets/10f247d4-21d7-4514-a752-43081368e48b" />
<h4>Answer: Shijiazhuang</h4>
<h3>Q3.Identifying the exploited script allows security teams to understand exactly which vulnerability was used in the attack. This knowledge is critical for finding the appropriate patch or workaround to close the security gap and prevent future exploitation. Can you provide the vulnerable PHP script name?</h3>
Filtering the attackers HTTP requests by using the filter ip.src==111.224.250.131 and http, the exploited page can be identified quicker. When further investigating the network traffic, the file
search.php raises suspicions as the attacker makes constant requests to it, the user-agent used to access the page is also named sqlmap, which upon some research, which is a tool that exploits SQL injection bugs.
<br><br>
<img width="1180" height="705" alt="image" src="https://github.com/user-attachments/assets/3ff93fc1-f66e-4bcf-9efe-037656c42e15" />
<h4>Answer:search.php</h4>
<h3>Q4.Establishing the timeline of an attack, starting from the initial exploitation attempt, what is the complete request URI of the first SQLi attempt by the attacker?</h3>
<h4>Note: Decode the Value.</h4>
Using the same filter of ip.src==111.224.250.131 and http, will help identify the first SQL injection attempt, right after the attackers request to access search.php (HTTP 1.1) the SQL injection attempt is made right after it, and the request URI is revealed to be /search.php?search=book%20and%201=1%20--%20-.
<br><br>
The value can be decoded by removing the %20, which are space, this produces the decoded value which is /search.php?search=book and 1=1; -- -
<br><br>
<img width="1181" height="812" alt="image" src="https://github.com/user-attachments/assets/7f70ca2d-99a9-426a-9c15-75c7c2c06861" />
<h4>Answer: /search.php?search=book and 1=1; -- -</h4>
<h3>Q5.Can you provide the complete request URI that was used to read the web server's available databases?</h3>
Upon research, databases contain key words such as "schema" or "information", so the filter that is best used to find these packets is http and contains "schema", this singles out lots of important packets that can be identified to find the correct complete request URI
<img width="1172" height="427" alt="image" src="https://github.com/user-attachments/assets/61635e20-2749-437d-855d-0e791c00cd0c" />
The packets that require attention are ones that have sqlmap as its useragent, as this was used to obtain the database, the 2nd packet 

