---
title: Verify that the current user is granted access in the appropriate allowAccounts section of SMSvcHost.exe.config
layout: post
comments: true
dsq_thread_id: 209 http://www.erata.net/?p=209
category: windows
---
Quick tip that maybe will save others time:

In Windows 7 ( and maybe Vista) when hosting a WCF Service in a console application AS A USER and not as administrator, you might get an exception telling you that you don't have access to register with the net.tcp port sharing service.

To resolve this problem:

- Download [PsGetSid][1] ( this will give you the [Security Identifier - SID][2] for your user or user group )

- Find and open SMSvcHost.exe.config ( usualy in c:\Windows\Microsoft.NET\Framework64\v4.0.30319\SMSvcHost.exe.config or c:\Windows\Microsoft.NET\Framework\v4.0.30319\SMSvcHost.exe.config )

- Run psgetsid.exe <youraccount> to get the SID ( should look something like this S-1-5-21-1754548885-2506776180-2303324228-4659 )

- By default the SMSvcHost.exe.config contains a section <system.serviceModel.activation> which has a <net.tcp> child which has a <allowAccounts> child. The IMPORTANT part if you are using an editor that does not do syntax highlight for XML - THE ABOVE SECTION IS COMMENTED by DEFAULT so adding child <allowAccounts> has not effect.

- YOU NEED TO ADD A NEW <system.serviceModel.activation> SECTION with it's children sections to make it work ( or uncomment the existing one and removing the // comments which are not valid xml ).

- Restart the net.tcp portsharing service and you are good to go.

In the end you should have something like this:

{% highlight xml linenos %}
< ?xml version="1.0" encoding="utf-8"?>
<!-- The configuration file for SMSvcHost.exe -->
<configuration>
    <runtime>
        <gcconcurrent enabled="false" />
    </runtime>
    <system.serviceModel>
        <diagnostics performanceCounters="Off" etwProviderId="{f18839f5-27ff-4e66-bd2d-639b768cf18b}"/>
    </system.serviceModel>
	<system.serviceModel.activation>
        <net.tcp listenBacklog="10" maxPendingConnections="100" maxPendingAccepts="2" receiveTimeout="00:00:10" teredoEnabled="false">
            <allowaccounts>
                <add securityIdentifier="S-1-5-21-1754548885-2506776180-2303324228-4659"/>
            </allowaccounts>
        </net.tcp>
    </system.serviceModel.activation>
</configuration>
{% endhighlight %}

Also you might need to [grant access][3] to the user to register as a listener for an url:

{% highlight text %}
netsh http add urlacl url=http://+: <port>/MyUri user=DOMAIN\user
{% endhighlight %}

I have to admin the most of the time i've spent on this issue was because i was modifying the commented section in the the config xml.

 [1]: http://technet.microsoft.com/en-us/sysinternals/bb897417 "PsGetSid"
 [2]: http://en.wikipedia.org/wiki/Security_Identifier "Security Identifier"
 [3]: http://msdn.microsoft.com/en-us/library/ms733768.aspx "Configuring HTTP and HTTPS"