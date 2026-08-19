<h1>Tools Used</h1>
Wireshark
<br>
VirusTotal
<h1>Scenario</h1>
The SOC team has detected suspicious activity in the network traffic, revealing that a machine has been compromised. Sensitive company information has been stolen. Your task is to use Network Capture (PCAP) files and Threat Intelligence to investigate the incident and determine how the breach occurred.
<h1>Questions</h1>
<h3>Q1.Which IP address was used by the attacker during the initial access?</h3>
Initially, the IP address 10.2.14.101 tries to access the website portfolo.serveirc.com, this is most likely a typo and indicates that the website they are accessing may not be the correct one. The DNS server 10.2.14.1 responds and returns the website for them to access. 
<br><br>
<img width="1920" height="373" alt="image" src="https://github.com/user-attachments/assets/eeb4a866-bc63-4a51-85e2-a9a9b95a370b" />
<br><br>
After the DNS is resolved, a connection is established between 10.2.14.101 and 62.173.142.148 with a three-way handshake (SYN→SYN-ACK→ACK), and data is being sent between the IP addresses, with 10.2.14.101 requesting the login page. 
<br><br>
But the IP address 62.173.142.148 is highly suspicious, searching it on VirusTotal reveals that it has been flagged a few times as malicious, confirming it to be the attacker.
<br><br>
<img width="1702" height="212" alt="image" src="https://github.com/user-attachments/assets/5281e79a-6615-4bd3-a8ff-9433f2be21c6" />
<h4>Answer: 62.173.142.148</h4>
<h3>Q2.What is the name of the malicious file used for initial access?</h3>
Reviewing the network traffic further, we see that 10.2.14.101 had requested the login page /login.php from 62.173.142.148, looking at the packet of the page that 10.2.14.101 received from 62.173.142.148 (HTTP/1.1 200 OK)  may reveal the malicious file that they had gotten.
<br><br>
<img width="1917" height="272" alt="image" src="https://github.com/user-attachments/assets/986a9bb1-d2b8-46b3-9c2c-6061f4e293cd" />
<br><br>
Reviewing the packet data shows that attached to the page was a JavaScript file named allegato_708.js, revealing the name of the malicious file that infected 10.2.14.101.   
<br><br>
<img width="960" height="442" alt="image" src="https://github.com/user-attachments/assets/45aa22f9-10ab-40ab-b258-4fc6326fb4d6" />
<h4>Answer: allegato_708.js</h4>
<h3>Q3.What is the SHA-256 hash of the malicious file used for initial access?</h3>
<h5>Warning! this step requires you to download a real malicious file to be able to complete this, its recommended that an isolated virtual machine is used for this step.</h5>
To further investigate the malicious nature of the file, the SHA-256 hash should be checked, to obtain the file in the first place, navigate to Wiresharks File→Export Objects→HTTP and export the login.php file.
<br><br>
<img width="855" height="627" alt="image" src="https://github.com/user-attachments/assets/8afc1b35-7245-4a67-a707-cff9b28442c8" />
<br><br>
Once the file has been obtained, the SHA-256 hash can be found by using the command line to reveal it. On Windows, inputting the command certutil -hashfile login.php sha256, the file hash is revealed to be 847b4ad90b1daba2d9117a8e05776f3f902dda593fb1252289538acf476c4268
<br><br>
<img width="652" height="81" alt="image" src="https://github.com/user-attachments/assets/8e4740fc-d89d-4c18-86af-b33ab53e37bc" />
<h4>Answer: 847b4ad90b1daba2d9117a8e05776f3f902dda593fb1252289538acf476c4268 </h4>
<h3>Q4.Which process was used to execute the malicious file?</h3>
After entering the obtained SHA-256 hash into the search section of the VirusTotal homepage, a closer look is given into the properties of the login.php file, the Behaviours section is the most important section for finding what allowed the malicious file to be executed.
<br><br>
<img width="1717" height="402" alt="image" src="https://github.com/user-attachments/assets/a533748a-6ca3-4239-98bb-a3f60bb59a2e" />
On Behaviours, scrolling down to the Process and service actions header reveals the processes that allowed the malicious allegato_708.js to run. There is one notable file process that suggests the root of the problem, the file paths "C:\Windows\system32\wscript.exe" "C:\Users\<USER>\Desktop\708.js" and C:\Windows\System32\wscript.exe C:\Windows\System32\WScript.exe "C:\Users\user\Desktop\708.js" reveals that as soon as the script was downloaded onto  10.2.14.101, the wscript.exe file was used to run the malicious script within the device and infect it.
<br><br>
<img width="1687" height="565" alt="image" src="https://github.com/user-attachments/assets/2ddfd2de-6d8a-4701-b248-90140a7d179a" />
<h4>Answer: wscript.exe</h4>
<h3>Q5.What is the file extension of the second malicious file utilized by the attacker?</h3>
further reviewing the network traffic reveals another DNS request made by 10.2.14.101 to the website sounddata.top, immediately after they establish communications, 10.2.13.101 requests the file resources.dll.
<br><br>
<img width="1178" height="211" alt="image" src="https://github.com/user-attachments/assets/06d9ce5f-ebf3-4f27-8bed-6a97f5cfac65" />
<h4>Answer: .dll</h4>
<h3>Q6.What is the MD5 hash of the second malicious file?</h3>
Repeat the command used to identify file hashes by inputting certutil -hashfile resources.dll md5
<br><br>
<img width="575" height="80" alt="image" src="https://github.com/user-attachments/assets/22858595-ed4d-4066-b71e-c36005f24ad6" />
<h4>Answer: e758e07113016aca55d9eda2b0ffeebe</h4>
 
