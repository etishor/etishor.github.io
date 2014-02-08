---
title: Verify that the current user is granted access in the appropriate allowAccounts section of SMSvcHost.exe.config
author: Iulian Margarintescu
layout: post
permalink: /windows/verify-that-the-current-user-is-granted-access-in-the-appropriate-allowaccounts-section-of-smsvchost-exe-config/
views:
  - 940
dsq_thread_id:
  - 307706076
categories:
  - windows
tags:
  - .NET
  - access
  - administrator
  - editor
  - hosting
  - http
  - lt system
  - Microsoft
  - Microsoft Vista
  - Microsoft Windows
  - Microsoft.NET
  - net.tcp
  - netsh
  - PsGetSid
  - security identifier
  - service
  - SMSvcHost.exe.config
  - user
  - wcf
  - WCF Service
  - windows 7
  - windows microsoft
  - xml
  - youraccount
---
Quick tip that maybe will save others time:

In Windows 7 ( and maybe Vista) when hosting a WCF Service in a console application AS A USER and not as administrator, you might get an exception telling you that you don&#8217;t have access to register with the net.tcp port sharing service.

To resolve this problem:

- Download [PsGetSid][1] ( this will give you the [Security Identifier &#8211; SID][2] for your user or user group )

- Find and open SMSvcHost.exe.config ( usualy in c:\Windows\Microsoft.NET\Framework64\v4.0.30319\SMSvcHost.exe.config or c:\Windows\Microsoft.NET\Framework\v4.0.30319\SMSvcHost.exe.config )

- Run psgetsid.exe <youraccount> to get the SID ( should look something like this S-1-5-21-1754548885-2506776180-2303324228-4659 )

- By default the SMSvcHost.exe.config contains a section <system.serviceModel.activation> which has a <net.tcp> child which has a <allowAccounts> child. The IMPORTANT part if you are using an editor that does not do syntax highlight for XML &#8211; THE ABOVE SECTION IS COMMENTED by DEFAULT so adding child <allowAccounts> has not effect.

- YOU NEED TO ADD A NEW <system.serviceModel.activation> SECTION with it&#8217;s children sections to make it work ( or uncomment the existing one and removing the // comments which are not valid xml ).

- Restart the net.tcp portsharing service and you are good to go.

In the end you should have something like this:

<pre class="brush:xml">&lt; ?xml version="1.0" encoding="utf-8"?>
<!-- The configuration file for SMSvcHost.exe -->
&lt;configuration>
    &lt;runtime>
        &lt;gcconcurrent enabled="false" />
    &lt;/runtime>
    &lt;system .serviceModel>
        &lt;diagnostics performanceCounters="Off" etwProviderId="{f18839f5-27ff-4e66-bd2d-639b768cf18b}"/>
    &lt;/system>
	&lt;system .serviceModel.activation>
        &lt;net .tcp listenBacklog="10" maxPendingConnections="100" maxPendingAccepts="2" receiveTimeout="00:00:10" teredoEnabled="false">
            &lt;allowaccounts>
                &lt;add securityIdentifier="S-1-5-21-1754548885-2506776180-2303324228-4659"/>
            &lt;/allowaccounts>
        &lt;/net>
    &lt;/system>
&lt;/configuration>
</pre>

Also you might need to [grant access][3] to the user to register as a listener for an url:

netsh http add urlacl url=http://+: <port>/MyUri user=DOMAIN\user

I have to admin the most of the time i&#8217;ve spent on this issue was because i was modifying the commented section in the the config xml. </port>

 [1]: http://technet.microsoft.com/en-us/sysinternals/bb897417 "PsGetSid"
 [2]: http://en.wikipedia.org/wiki/Security_Identifier "Security Identifier"
 [3]: http://msdn.microsoft.com/en-us/library/ms733768.aspx "Configuring HTTP and HTTPS"