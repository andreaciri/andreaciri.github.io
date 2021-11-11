+++
title = "Incidents"
date = "2021-10-15"
author = "Andrea Cirigliano"
cover = "img/photo.jpeg"
description = "L obiettivo della gestione di un incidente è quella di comunicare a tutta l'azienda cosa sta succedendo"
+++

The purpose of incident management is to communicate to the entire company what is going on, and to protect who is working to resolve it.

I remember the first times in the office during an incident. Our website is completely down and a crowd is assembling behind the shoulders of a single dev or sysadmin.

> "How much it takes to solve it?"
> "How much money are we loosing?"

The stress and the pressure can be oppressive. A good incident management should be used to filter requests and questions from people interested in the resolution, and at the same time to ease the work of who is actually fixing it.

In [Subito](https://www.subito.it) we embraced since a long time the mantra *You build it, you run it*. We, software engineers, are responsible for what we develop and thus we deploy it. And if it doesn't work we fix it. Nights included.

### on-calls

A good starting point for your incident management is to create an on-call schedule. We organize on-calls per team and level. In every moment a team has a 1st level and a 2nd level on-call.

The 1st level will be paged first. We use [PagerDuty](https://www.pagerduty.com/) for this.
This is how I configured PagerDuty notifications in case of incident:

![PagerDuty notifications screenshot](/img/incidents_pd_notifications.png "PagerDuty notifications screenshot")

A first push is sent after 2 minutes. If the incident is transient and the ticket autocloses within 2 minutes, my sleep is saved, I don't get paged at all. Further notifications are useful to storm the phone in case I missed the first sound.

Usually, the 1st level on-call has a broader knowledge and at a higher level. When he is not able to resolve the incident on his own, he has the right to engange the 2nd level.

Let's make an example. 1st level on-call is a senior fullstack engineer with a good knowledge of the infrastructure and how users traffic travels the CDN, proxies, FE, BE and storages. Most of the times the incident is caused by a network issue, an external dependency or a bad configuration. He is able to resolve these incidents autonomously.

It may happen that the incident has an applicative root cause, like a bug or an unexpected data to process. The 2nd level on-call - a mid level engineer - has less experience in general but is more up-to-date on the development and can identify more easily a resolution like a rollback, an hotfix or a feature toggle.

### Roles


La prima cosa per la gestione dell' incidente è una singola source of Truth riconosciuta in tutta l'azienda. Se viene dichiarato un incidente ( automatico o manuale) è necessario un singolo tool universalmente riconosciuto in azienda da cui è possibile leggere lo stato dell'incidente. Ad esempio se qualcuno ha cominciato a lavorarci. 

