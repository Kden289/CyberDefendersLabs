<h1>Tools Used</h1>
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
<br><br>
<img width="1172" height="427" alt="image" src="https://github.com/user-attachments/assets/61635e20-2749-437d-855d-0e791c00cd0c" />
<br><br>
The packets that require attention are ones that have sqlmap as its useragent, as this was used to obtain the database, the 2nd packet is the most interesting as its request uri reads /search.php?search=book%27%20UNION%20ALL%20SELECT%20NULL%2CCONCAT%280x7178766271%2CJSON_ARRAYAGG%28CONCAT_WS%280x7a76676a636b%2Cschema_name%29%29%2C0x7176706a71%29%20FROM%20INFORMATION_SCHEMA.SCHEMATA--%20-, and when I decoded it using a url decoder, it returned /search.php?search=book' UNION ALL SELECT NULL,CONCAT(0x7178766271,JSON_ARRAYAGG(CONCAT_WS(0x7a76676a636b,schema_name)),0x7176706a71) FROM INFORMATION_SCHEMA.SCHEMATA-- -
<br><br>
The FROM shows that the attacker is trying to enumerate information of the database.
<br><br>
<img width="926" height="885" alt="image" src="https://github.com/user-attachments/assets/4a45ddfe-96e2-40e5-adb7-66cadc409252" />
Answer: /search.php?search=book' UNION ALL SELECT NULL,CONCAT(0x7178766271,JSON_ARRAYAGG(CONCAT_WS(0x7a76676a636b,schema_name)),0x7176706a71) FROM INFORMATION_SCHEMA.SCHEMATA-- -
<h3>Q6.Assessing the impact of the breach and data access is crucial, including the potential harm to the organization's reputation. What's the table name containing the website users data?</h3>
The keyword UNION I found out is a very large indicator of a SQLi, because its difficult to filter SQLi attempts and the servers response to it, I use the filter http contains "UNION" or ip.src== 73.124.22.98 to cover both the attacks SQLi attempts and the servers responses, then navigate to the attackers attempts to find out what the server responded with.
<br><br>
Having added user.agent as a column, reviewing the network traffic didn't take long to show the servers responses to the SQLi attempts and investigating the individual packets revealed users data to be labelled as "Customers".
<br><br>
<img width="1182" height="543" alt="image" src="https://github.com/user-attachments/assets/71899c48-b6de-4b16-a210-c95d287fa017" />
<h4>Answer: Customers</h4>
<h3>Q7.The website directories hidden from the public could serve as an unauthorized access point or contain sensitive functionalities not intended for public access. Can you provide the name of the directory discovered by the attacker?</h3>
I used the filter file.requests.uri to filter all url requests in the network traffic. After the attacker reads the companies database, they start to enumerate the servers files using the gobuster application, it go through several directories, with the server naturally returning 404 Not Found for hidden directories each time, but this is until they go through the /admin/ path, where the server returns an 302 FOUND then an OK to show that its let the attacker through, revealing the vulnerable directory.
<br><br>
<img width="1177" height="422" alt="image" src="https://github.com/user-attachments/assets/f893aa25-e42e-49bc-8a8f-d9d15a4b5d60" />
<h4>Answer: /admin/</h4>
<h3>Q8.Knowing which credentials were used allows us to determine the extent of account compromise. What are the credentials used by the attacker for logging in?</h3>
I used the filter file.requests.uri and ip.src 111.224.250.131 to look through requests made by the hacke, and navigated to after they had gained access to the /admin/ directory. At the end of the network traffic, there are 4 requests made at login.php, suggesting that the attacker made 4 attempts at logging in before eventually getting the password correct, so the last request is the one that needs to be focused on.
<br><br>
<img width="1180" height="177" alt="image" src="https://github.com/user-attachments/assets/06cf4e37-77bc-49e8-9b7d-494d68304af2" />
When looking at the packet data of the last request, information on what was sent is shown, revealing both the username and password to be admin and admin123!
<br><br>
<img width="925" height="292" alt="image" src="https://github.com/user-attachments/assets/3f1ebb1b-bbbe-405a-9251-89cc1591f108" />
<h4>Answer: admin:admin123!</h4>
<h3>Q9.We need to determine if the attacker gained further access or control of our web server. What's the name of the malicious script uploaded by the attacker?</h3>
Following off where the attacker has successfully logged in to the compromised account, they access the index.php page, and then suspiciously sends it back to the server, further investigating the packet data of this file reveals that is contains the MIME Multipart Media Encapsulation section, which from my knowledge is commonly used by malicious users to hide malicious files within files to avoid raising suspicion on a network.
<br><br>
Looking into the MIME Multipart Media Encapsulation section reveals a part named "fileToUpload" which contains the filename "NVri2vhp.php", this is malicious script that I am looking for that is being hidden.
<br><br>
<img width="1172" height="396" alt="image" src="https://github.com/user-attachments/assets/aa273aa5-bfb1-4ffb-b978-5560efaf2aea" />
<img width="588" height="361" alt="image" src="https://github.com/user-attachments/assets/fd76fa3f-fce5-4a58-918b-6c7852e4c294" />
<h4>Answer:NVri2vhp.php</h4>
