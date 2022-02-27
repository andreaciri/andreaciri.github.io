+++
title = "How you should handle incidents in your team or organization"
date = "2021-11-14"
author = "Andrea Cirigliano"
cover = "img/candle.jpeg"
description = "Setup an incident management for your team or organization."
+++

I remember the first times in the office during an incident. Our website is completely down and a crowd is assembling behind the shoulders of a single dev or sysadmin.

> "How much it takes to solve it?"  
> "How much money are we loosing?"

The stress and the pressure can be oppressive. A good incident management should be used to filter requests and questions from people interested in the resolution, and at the same time to guide the activity of who is actually fixing it.

In [Subito](https://www.subito.it) we embraced since a long time the mantra *You build it, you run it*. We, software engineers, are responsible for what we develop and thus we deploy it. And if it doesn't work we fix it. Nights included.

### On-calls

A good starting point for your incident management is to create an on-call schedule. We organize on-calls per team and level. In every moment a team has a 1st level and a 2nd level on-call.

The 1st level will be paged first. We use [PagerDuty](https://www.pagerduty.com/) for this.
This is how I configured my PagerDuty notifications in case of incident:

![PagerDuty notifications screenshot](/img/incidents_pd_notifications.png "PagerDuty notifications screenshot")

A first push is sent after 2 minutes. If the incident is transient and the ticket auto-closes within 2 minutes then my sleep is saved, I don't get paged at all. Further notifications are useful to storm the phone in case I missed the first sound.

Usually, the **1st level** on-call has a broader knowledge and at a higher level. When he is not able to resolve the incident on his own, he has the right to engage the **2nd level**.

Let's make an example. 1st level on-call is a senior fullstack engineer with a good knowledge of the infrastructure and how user traffic travels various CDNs, proxies, FEs, BEs and storages. Most of the times the incident is caused by a network issue, an external dependency or a bad configuration. He is able to resolve such incidents autonomously.

It may happen that the incident has an application level root cause, like a bug or an unexpected data to process. The 2nd level on-call - a mid level engineer - has less experience in general but is more up-to-date on the development and can identify more easily a resolution like a rollback, an hotfix or a feature toggle.

### Roles

You are the 1st level and you receive the notification. Congrats, you are the **Incident Commander**. 
You have the responsibility of the incident status, so put the ticket in WIP / acknowledged / whatever so anyone inside your org knows that you are working on it.

For small incidents, the Incident Commander is also the **Communication Lead**. You have to update your ticket with your findings during the troubleshooting.

Incident commander has also the power to reassign roles to new responders. When the incident is large enough the Communication Lead can be reassigned to a colleague.

### Communication

All the meaningful progresses should be reported in a single place recognized as the source of truth in your organization. Choose a source of truth that fits well for you, like a ticketing system or a live document (Google doc).

The Communication Lead may need to communicate and coordinate with external figures, like other tech teams or product stakeholders. Whenever the impact of the incident is relevant I suggest to create a public Slack channel that anyone interested can join.
I've seen many times inexperienced developers coordinate the incident using Slack private channels, reusing development chats.

**DO NOT USE PRIVATE SLACK CHANNELS TO HANDLE THE INCIDENT**.

This prevents visibility across the company, reduce knowledge sharing on how to resolve that kind of incident, and can cause plenty of misunderstandings.

As a downside, larger incidents will attract a lot of attention internally. While assistance to the Incident Commander is always appreciated, it’s important to keep the chatter in the troubleshooting channels to a minimum.  
During the Facebook incident of October 2021, that brought the entire Zuckerberg ecosystem down, an [employee disclosed confidential information on Reddit](https://news.ycombinator.com/item?id=28749244) with details of the incident.  
If you’re not actively representing a service or participating in resolving the incident, but purely there as a curious observer - please be mindful of not polluting the channel and keep information for yourself.

If your outage is exposed to the public it is good practice to update a status page and write a short description. It's better to avoid details, just a general description of the impact and the estimated time to recover.  
This is the report of the [global CDN disruption](https://status.fastly.com/incidents/vpk0ssybt3bj) experienced from Fastly, that stopped half of the entire Internet in June 2021.

![Faslty global outage report screenshot](/img/fastly_report.png "Faslty global outage report screenshot")

There are very few details but we could understand when the incident was mitigated and what could be the residual effects.

### Resolution
When your incident can be closed, take a break and then reserve some minutes for an **Incident Report**. Most of the report can be extracted from the troubleshooting channel, but make sure you clean it up, remove wrong assumptions and useless information. Use screenshots, personal notes, logs, graphs.  
If needed, schedule a **Post Mortem** with the incident stakeholders. During this meeting you should agree on the root cause analysis. Evaluate the risk of this incident occurring again. Ask yourself if actions can be taken to improve the resolution or avoid the incident at all.


Organizations facing large scale incident exists since many years and they developed a strong know-how. They are firefighters, police and medical services. We can and should learn from their experience, and create a command structure that is similar in our tech companies.