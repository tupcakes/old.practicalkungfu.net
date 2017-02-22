---
id: 245
title: Exchange 2010 and Mcafee Security for Microsoft Exchange
date: 2013-01-17T16:57:01+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=245
permalink: /2013/01/17/exchange-2010-and-mcafee-security-for-microsoft-exchange/
categories:
  - Exchange
  - Security
---
Had an issue today. We installed Mcafee Security for Exchange (which I will from here on out just call mcafee since it&#8217;s hard to type out).

The install went mostly well on the servers, however we noticed that the Exchange transport service didn&#8217;t start back up after the install on one of the servers. When starting the transport service and trying to use the queue view we got an error. Additionally the following errors appeared in the event log.

<pre class="lang:batch decode:true brush: text; gutter: false ">Log Name:      Application
Source:        MSExchange Extensibility
Date:          1/17/2013 4:19:11 PM
Event ID:      1052
Task Category: MExRuntime
Level:         Error
Keywords:      Classic
User:          N/A
Computer:      Exchserver
Description:
Failed to create agent factory for the agent 'McAfeeTxAgent' with error 'Failed to create type 'McAfee.E12Agents.McTxAgent.McAfeeTxAgentFactory' from assembly 'C:\Program Files (x86)\McAfee\MSME\bin\E2007 Agents\McTxAgentX64.dll' due to error 'Invalid agent assembly path.'.'. Please verify the corresponding transport agent assembly and dependencies with correct version are installed.
Event Xml:
&lt;Event xmlns="http://schemas.microsoft.com/win/2004/08/events/event"&gt;
  &lt;System&gt;
    &lt;Provider Name="MSExchange Extensibility" /&gt;
    &lt;EventID Qualifiers="49156"&gt;1052&lt;/EventID&gt;
    &lt;Level&gt;2&lt;/Level&gt;
    &lt;Task&gt;1&lt;/Task&gt;
    &lt;Keywords&gt;0x80000000000000&lt;/Keywords&gt;
    &lt;TimeCreated SystemTime="2013-01-17T22:19:11.000000000Z" /&gt;
    &lt;EventRecordID&gt;788098&lt;/EventRecordID&gt;
    &lt;Channel&gt;Application&lt;/Channel&gt;
    &lt;Computer&gt;Exchserver&lt;/Computer&gt;
    &lt;Security /&gt;
  &lt;/System&gt;
  &lt;EventData&gt;
    &lt;Data&gt;McAfeeTxAgent&lt;/Data&gt;
    &lt;Data&gt;Failed to create type 'McAfee.E12Agents.McTxAgent.McAfeeTxAgentFactory' from assembly 'C:\Program Files (x86)\McAfee\MSME\bin\E2007 Agents\McTxAgentX64.dll' due to error 'Invalid agent assembly path.'.&lt;/Data&gt;
  &lt;/EventData&gt;
&lt;/Event&gt;


Log Name:      Application
Source:        MSExchangeTransport
Date:          1/17/2013 4:19:11 PM
Event ID:      16023
Task Category: Configuration
Level:         Error
Keywords:      Classic
User:          N/A
Computer:      Exchserver
Description:
Microsoft Exchange couldn't start transport agents. The Microsoft Exchange Transport service will be stopped. Exception details: Failed to create type 'McAfee.E12Agents.McTxAgent.McAfeeTxAgentFactory' from assembly 'C:\Program Files (x86)\McAfee\MSME\bin\E2007 Agents\McTxAgentX64.dll' due to error 'Invalid agent assembly path.'. : Microsoft.Exchange.Data.ExchangeConfigurationException: Failed to create type 'McAfee.E12Agents.McTxAgent.McAfeeTxAgentFactory' from assembly 'C:\Program Files (x86)\McAfee\MSME\bin\E2007 Agents\McTxAgentX64.dll' due to error 'Invalid agent assembly path.'. ---&gt; System.ArgumentException: Invalid agent assembly path.
   --- End of inner exception stack trace ---
   at Microsoft.Exchange.Data.Transport.Internal.MExRuntime.FactoryTable.CreateAgentFactory(AgentInfo agentInfo)
   at Microsoft.Exchange.Data.Transport.Internal.MExRuntime.FactoryTable..ctor(IEnumerable agents)
   at Microsoft.Exchange.Data.Transport.Internal.MExRuntime.RuntimeSettings..ctor(MExConfiguration config, String agentGroup)
   at Microsoft.Exchange.Data.Transport.Internal.MExRuntime.MExRuntime.Initialize(String configFile, String agentGroup, Boolean isBridgeHead, String installPath)
   at Microsoft.Exchange.Transport.Extensibility.AgentComponent.Load()
Event Xml:
&lt;Event xmlns="http://schemas.microsoft.com/win/2004/08/events/event"&gt;
  &lt;System&gt;
    &lt;Provider Name="MSExchangeTransport" /&gt;
    &lt;EventID Qualifiers="49156"&gt;16023&lt;/EventID&gt;
    &lt;Level&gt;2&lt;/Level&gt;
    &lt;Task&gt;16&lt;/Task&gt;
    &lt;Keywords&gt;0x80000000000000&lt;/Keywords&gt;
    &lt;TimeCreated SystemTime="2013-01-17T22:19:11.000000000Z" /&gt;
    &lt;EventRecordID&gt;788099&lt;/EventRecordID&gt;
    &lt;Channel&gt;Application&lt;/Channel&gt;
    &lt;Computer&gt;Exchserver&lt;/Computer&gt;
    &lt;Security /&gt;
  &lt;/System&gt;
  &lt;EventData&gt;
    &lt;Data&gt;Failed to create type 'McAfee.E12Agents.McTxAgent.McAfeeTxAgentFactory' from assembly 'C:\Program Files (x86)\McAfee\MSME\bin\E2007 Agents\McTxAgentX64.dll' due to error 'Invalid agent assembly path.'.&lt;/Data&gt;
    &lt;Data&gt;Microsoft.Exchange.Data.ExchangeConfigurationException: Failed to create type 'McAfee.E12Agents.McTxAgent.McAfeeTxAgentFactory' from assembly 'C:\Program Files (x86)\McAfee\MSME\bin\E2007 Agents\McTxAgentX64.dll' due to error 'Invalid agent assembly path.'. ---&gt; System.ArgumentException: Invalid agent assembly path.
   --- End of inner exception stack trace ---
   at Microsoft.Exchange.Data.Transport.Internal.MExRuntime.FactoryTable.CreateAgentFactory(AgentInfo agentInfo)
   at Microsoft.Exchange.Data.Transport.Internal.MExRuntime.FactoryTable..ctor(IEnumerable agents)
   at Microsoft.Exchange.Data.Transport.Internal.MExRuntime.RuntimeSettings..ctor(MExConfiguration config, String agentGroup)
   at Microsoft.Exchange.Data.Transport.Internal.MExRuntime.MExRuntime.Initialize(String configFile, String agentGroup, Boolean isBridgeHead, String installPath)
   at Microsoft.Exchange.Transport.Extensibility.AgentComponent.Load()&lt;/Data&gt;
  &lt;/EventData&gt;
&lt;/Event&gt;</pre>

Transport agents you say? I ran Get-TransportAgent and got the following.

<pre class="brush: shell; gutter: false">[PS] C:\Windows\system32&gt;Get-TransportAgent

Identity                                           Enabled         Priority
--------                                           -------         --------
Transport Rule Agent                               True            1
Text Messaging Routing Agent                       True            2
Text Messaging Delivery Agent                      True            3
McAfeeTxRoutingAgent                               False           4
McAfeeTxAgent                                      False           5</pre>

I figured what the heck, it doesn&#8217;t work. So I tried disabling the mcafee agents for the time being.

<pre class="brush: powershell; gutter: false">Disable-TransportAgent McAfeeTxRoutingAgent
Disable-TransportAgent McAfeeTxAgent</pre>

After disabling the mcafee agents I restarted the Exchange transport service and was able to successfully connect to to the queue.

While this doesn&#8217;t fix the mcafee issue, it does at least get mail flowing again until the mcafee issue can be addressed. My guess is it will require a complete removal and reinstall of mcafee.