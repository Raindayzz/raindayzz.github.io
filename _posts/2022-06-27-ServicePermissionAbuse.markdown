---
layout: post
title:  "Abusing Service Permissions"
date:   2022-06-20 12:44:14 -0400
categories: TechnicalBlog
---

This blog will show a bit into the service permission abuse and how weak or insecure permissions can be leveraged to exploit something trivial.

I won't show how I identified this as I don't have the screenshot and would like to focus on the exploit.

### SNMPTRAP Service

The follow screenshot demonstrates the service. You may note, it is disabled by default, and is running as a default configuration account when creating a service. There are MULTIPLE ways to exploit something like this, especially when you have the permissions to modify all configurations of the service. 

![pastedimage.png](/assets/images/ServicePerm/scqc.png)

First thing I noticed is the service is disabled.. This cannot be started/stopped without being enabled first so we launch services (```Win+R and type services.msc```)

Identify the snmptrap service and open the properites.

![pastedimage1.png](/assets/images/ServicePerm/servicesmsc.png)



![pastedimage2.png](/assets/images/ServicePerm/disable.png)

Enable the service (you can also do this from cmdline but this service.msc is good to know as it can be helpful during pentests)

![pastedimage3.png](/assets/images/ServicePerm/enable.png)

As identified in the first image, the service is using localService, this won't cut it (found this out by trial and error), we need to set this to the local system account in order to abuse this to the level in which we want to.

![pastedimage4.png](/assets/images/ServicePerm/setaccount.png)

Powerup.ps1 has a function module that can exploit the binPath to create a user and add a local administrator user (Not domain joined) which was exactly what I needed at the time.

https://github.com/PowerShellMafia/PowerSploit/blob/master/Privesc/PowerUp.ps1

Import the module - ```Import-Module PowerUp.ps1```

![pastedimage5.png](/assets/images/ServicePerm/invoke.png)

The exploit worked, running a few net user commands on the local machine (not domain) shows that the john user was added succesfully.

![pastedimage6.png](/assets/images/ServicePerm/proof.png)


This is a trivial Priv Esc that might come in handy to me, you, or anyone in the world that finds a similar vuln 


### References:

[https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/sc-config](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/sc-config)

[https://docs.microsoft.com/en-us/windows/win32/services/localservice-account](https://docs.microsoft.com/en-us/windows/win32/services/localservice-account)