---
layout: post
title:  "Day In The Life - Cyber Threat Hunter"
date:   2021-11-12 12:44:14 -0400
categories: blog
---

# Overview and Disclaimer

This blog will bascially talk about what I do on a "normal" day. **Disclaimer that this can vary from a pentest to use case development to EDR troubleshooting**

Since I work for a large company as a consultant I can get pull on to any project or engagment to provide a cyber service. This blog will basically talk about a normal day on my primary client that is a long term engagmnet

### Morning 08:30AM - 12:00

Not much of an early riser. I normally get up and rag myself to my desk to fire things up at 08:29. I fire up the coffee and get to it.

Day starts at 09:00 with a stand up meeting. Basically I'm engulfed with the client team of cyber engineers and we work in "sprints" (Agile, right? lmao) where we cover open tasks for the week and what we are working on. I don't partake too much besides the update I give on what I am working on.

That lasts till about 09:30 and I hop off of the call. Most of my meetings normally start with troubleshooting issues that are still open of came overnight from the SOC. They are also fufilled with content SIEM, threat intelligence, SOC, Client progress, Consultant sync, etc. These can really eat up some of my time and normally a messy part of the day. Not much I can really write about or give examples to not disclose, just know they can be boring or really productive. 

### Lunch 12:00 - 12:15 

This is normally just a sandwhich + smoothie and watch some youtube. Quick lunch, not waisting much time here.

### Afternoon 12:20 - 17:00

This is where the meat & potatoes happens. I tend to be free in the early afternoon to really get after some work. Let's give an example of what I did the other day. 

So my job is to hunt down new threats or TTPs in the client enviroment and provide insight on APTs and provide more detection coverage. Easy right? (Crank a Diet Mtn Dew)

While checking some articles on twitter and securtity blogs, I found something that peaked my interest. A new IOC (Indicator of compromise) to look for in a threat hunting manner. 

The thing I wanted to look into is file creations / process creations from the Recycle bin on our windows workstations. First thing to do was catch up on the IOCs found in this sweet article. (https://www.cybereason.com/blog/deadringer-exposing-chinese-threat-actors-targeting-major-telcos). Cyberreason has really good articles with examples of new TTPs being used in the wild. Easy enough to find right? Let's start with sysmon


Hunt for: cmd/powershell/scripts/LOLBins execution with MS Exchange/IIS parent processes (4688, Sysmon EID 1).
specific China Chopper web shell command-line (4688, Sysmon EID 1).
process creation from Recycle Bin (4688, Sysmon 1). 


Let's focus on the recycle bin. We isolated our WEC logs and looked for something like:

**I can't share too much about the client so sorry for the pseudo code**

WEC code = 4688
Path = C:\RecycleBin\<SID>\*
OR
Sysmon=1 
Path = C:\RecycleBin\<SID>\*


This returned some stuff we didn't want. I filtered out the BS or stuff that didn't really make sense. There were 2 creations that were really weird to my eye and I couldn't find out. I pinged our SOC shift lead at the client and informed he/she about the search I was looking for and they took over the investigation. 

I then pulled up our SIEM. It's time to write a rule for this. Boom cranked out the rule. Similar to what you can see on perhaps SPLUNK/Elastic:

```
process where event.type in ("start", "process_started", "info") and
process.name : ("wscript.exe", "cscript.exe",
"rundll32.exe", "regsvr32.exe",
"cmstp.exe", "RegAsm.exe",
"installutil.exe", "mshta.exe",
"RegSvcs.exe", "powershell.exe",
"pwsh.exe", "cmd.exe") and /*
add suspicious execution paths here */ process.args :
"C:\\$Recycle.Bin\\*") and not process.parent.executable : ("C:\\WIN
DOWS\\System32\\DriverStore\\FileRepository\\*\\igfxCUIService*.exe",
"C:\\Windows\\System32\\spacedeskService.exe",
"C:\\Program Files\\Dell\\SupportAssistAgent\\SRE\\SRE.exe") and not
(process.name : "rundll32.exe" and process.args : ("uxtheme.dll,#64",
"PRINTUI.DLL,PrintUIEntry"))
```

This is a high level of what I do all day! It's hard to give examples without giving screenshots or evidence from the client. 

### End Of Day Sync

My day normally ends with an EOD sync with my consulting workstream for the client. So there is around 6 of us that work for this client in their cyber program (Log Help, EDR, Threat hunting, GCP, Threat automation, etc).

We talk about tangible goals and updates from the day. We do it everyday and it's always to have a good meeting with our internal consultant team to make sure we are providing top of the line work for the client. 