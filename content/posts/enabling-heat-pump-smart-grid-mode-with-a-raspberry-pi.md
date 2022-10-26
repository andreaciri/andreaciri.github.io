+++
title = "Enabling Heat Pump Smart Grid Mode With a Raspberry Pi"
date = "2022-10-24"
author = "Andrea Cirigliano"
cover = "img/grid-sun.jpg"
description = "How I control my Heat Pump Smart Grid input using a Raspberry Pi"
+++
<!-- cover = "img/sg-ready-icon.jpg" -->
<!-- coverCaption = "If your heat pump shows this label it supports Smart Grid mode." -->

For the first time, I live in a house with an electric heat pump (natural gas heaters are still the most common here in Italy).\
As I always do when I have some kind of new machinery I immediately leaf through the manual. Note: this does not apply to software, for which I read the documentation only after several failures 😅.


Inside the manual there's a section called *Smart Grid Ready*. The idea is to simply tell the heat pump when there is free electricity or when the electricity is cheaper, to raise the set point temperature.\
I imagined this as a thermal battery where the house stores energy when it's convenient and releases it when necessary.

![Smart Grid Ready Manual Section](/img/smart-grid-ready-manual.jpg "Smart Grid Ready Manual Section")
*Smart Grid Ready pinout on the heat pump manual. I used the **Switch-on recommendation** mode.*

For once a smart feature seemed really smart, so I decided to make it work with my solar panel system.\
I have a SolarEdge inverter and after a quick search, I found it supports Modbus communication over TCP. There are plenty of [libraries](https://github.com/nmakel/solaredge_modbus) on GitHub to read real-time data directly from the inverter.\
Unfortunately, my inverter is connected to the network via WiFi instead of Ethernet, and SolarEdge recently [disabled Modbus over WiFi](https://github.com/binsentsu/home-assistant-solaredge-modbus/issues/67#issuecomment-998212321) for security reasons. So I had to use the [SolarEdge APIs](https://www.solaredge.com/sites/default/files//se_monitoring_api.pdf), making http requests to their cloud platform.

This is a sketch of how it works:

![Diagram of the project](/img/smart-grid-pi-diagram2.png "Diagram of the project")


I wrote a program in go that runs on the Raspberry. It's [available on my GitHub](https://github.com/andreaciri/smart-grid-pi). It requests the current power flow to SolarEdge and commands a relay connected to the Smart Grid input of the heat pump.\
SolarEdge only offers 300 daily requests to their APIs. Running the script only during daylight I can collect a sample every 2 minutes, enough for my goal.


Since my heat pump uses up to 2 kW when heating water, I used this value as the threshold to consider the power surplus enough for the smart grid toggle.

I also attached a LED semaphore to the Pi:
- 🔴 Red: no production at all
- 🟡 Yellow: production, but not enough
- 🟢 Green: enough free electricity, smart grid ON 🤟

This is a picture of the Raspberry with the semaphore and the relay, running inside a cotton buds plastic case on top of the heat pump internal unit:
 