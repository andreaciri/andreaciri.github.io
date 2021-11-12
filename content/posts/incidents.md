+++
title = "How you should handle incidents in your team or organization"
date = "2021-10-15"
author = "Andrea Cirigliano"
cover = "img/candle.jpeg"
description = "Setup an incident management in your team or organization"
+++

The purpose of incident management is to communicate to your company and your users what is going on, and to protect who is working to resolve it.

I remember the first times in the office during an incident. Our website is completely down and a crowd is assembling behind the shoulders of a single dev or sysadmin.

> "How much it takes to solve it?"
> "How much money are we loosing?"

The stress and the pressure can be oppressive. A good incident management should be used to filter requests and questions from people interested in the resolution, and at the same time to ease the work of who is actually fixing it.

In [Subito](https://www.subito.it) we embraced since a long time the mantra *You build it, you run it*. We, software engineers, are responsible for what we develop and thus we deploy it. And if it doesn't work we fix it. Nights included.

### on-calls

A good starting point for your incident management is to create an on-call schedule. We organize on-calls per team and level. In every moment a team has a 1st level and a 2nd level on-call.

The 1st level will be paged first. We use [PagerDuty](https://www.pagerduty.com/) for this.
This is how I configured my PagerDuty notifications in case of incident:

![PagerDuty notifications screenshot](/img/incidents_pd_notifications.png "PagerDuty notifications screenshot")

A first push is sent after 2 minutes. If the incident is transient and the ticket autocloses within 2 minutes then my sleep is saved, I don't get paged at all. Further notifications are useful to storm the phone in case I missed the first sound.

Usually, the 1st level on-call has a broader knowledge and at a higher level. When he is not able to resolve the incident on his own, he has the right to engange the 2nd level.

Let's make an example. 1st level on-call is a senior fullstack engineer with a good knowledge of the infrastructure and how user traffic travels various CDNs, proxies, FEs, BEs and storages. Most of the times the incident is caused by a network issue, an external dependency or a bad configuration. He is able to resolve such incidents autonomously.

It may happen that the incident has an applicative root cause, like a bug or an unexpected data to process. The 2nd level on-call - a mid level engineer - has less experience in general but is more up-to-date on the development and can identify more easily a resolution like a rollback, an hotfix or a feature toggle.

### Roles

You are the 1st level and you receive the notification. Congrats, you are the **Incident commander**. 
You have the responsibility of the incident status, so put the ticket in WIP / acknowledged / whatever so anyone inside your org knows that you are working on it.

For small incidents, the incident commander is also the **Communication Lead**, so during the troubleshooting update your ticket with your findings.

Incident commander has also the power to reassign roles to new responders. When the incident is large enough the communication lead can be reassigned to a colleague.

### Communication

The first communication about the incident progress should be done on your ticketing system. This should be recognized in your organization as the source of truth.
The communication lead may need to communicate the progress to other, like product stakeholders or 
