---
title: Hard data for heat pumps
tags:
  - stem
categories:
  - home-automation
---


A massive proportion of UK carbon emissions are from heating our homes[^1] - so I recently decided to do the right thing and consider a heat pump to replace our domestic gas boiler.

My energy supplier, Octopus, were on a mission to roll out heat pumps and had offered me a survey. Hurrah! But then they told me I couldn't have one. Booo! Here's my story.

# Why Octopus said "no"

After they made a very detailed survey of my house, measuring every room, their calculations said that I needed a 15kW capacity heat pump. This means that by their calculations, my house is losing 15kW of heat power to the outside world - under typical UK winter conditions.

If "15kW" (or indeed any kW) doesn't make sense to you yet, don't worry. Just know that 15kW is a _huge_ amount of heating, and too big for Octopus largest heat pump to cope with. So they said I couldn't have one, and (incredibly promptly to their credit) refunded me my survey fee.

# Why I knew their maths was amiss

I have our gas meter linked in to my home automation, so have accurate gas usage figures for every 30 seconds over the last 4 years. If I pick a really cold January day in the past, when the heating has been on full, here's my gas usage:

![Graph showing winter gas at 8kW](gas-usage.png)

Other than the odd spike in the data, it shows in the worst-case we put 8kW of heat into the house. But Octopus calculated 15kW of heat would be escaping the house. Without bandying-around any laws of thermodynamics, it's obvious that we can't be creating energy from nothing. If 8 is what's going in, then at most 8 is escaping to the outside.

But this was just a quick reckoning using one day of data. Admittedly a worst-case day, but still a very small sample. How could I use some hard data to properly prove this? How can I prove Octopus wrong? As Mark Watney would say in The Martian: "I'm gonna have to science the shizz out of this"

# Setting our hypotheses

There are at least two ways that Octopus could have over-calculated my energy use. I need to do some science on each.

### 1. I'm running the house colder than they expect.

This is partly true, because currently we only heat the parts of the house that we're using at that time. Like only heating bedrooms during the evening, and letting the overall temperature drop during the daytime if we're out at work. Heat pumps don't work so well with that approach because they can't rapidly heat a room, so you need to have a higher baseline temperature.

I can answer this by simulating what would happen if I had higher temperatures in each room consistently, by building a physics model for it.

### 2. The house is better insulated than they expect.

This is definitely true, because Octopus group all houses into "pre-2000" or "2001 onwards" and assume an average insulation based on that. My house built in 1999 has an insulation rating that's around 80% as good as a modern building... But Octopus lump me in with the same calculation as for Victorian buildings that lose 4x as much heat. And heat loss _is_ your energy cost.

I can answer this by measuring how fast each room heats up and cools down for an amount of heat input, and fitting a mathematical model to it.

# STEM to the rescue

![alt text](thermostat.png)

I'm a big advocate of STEM for getting people excited about the world around them, by bringing different skills together to solve interesting problems. In our case:

- **S**cience. Mostly some basic physics, like the fact that heat moves from a hot place to a colder one. And this happens faster when the temperature difference is greater - so you lose more heat from the house when it's colder outside. This is intuitively true, right? For your gas bill, cold days cost more.

- **T**echnology. We can use readily-available home automation tech to record the room temperatures.
- **E**ngineering. In this case, software engineering. We can write some computer programs to collect and process the data, and run simulations on them.
- **M**aths. Maths is everywhere! We'll use it for our simulations.

# Physics: the theoretical model

I found an amazing open-source project called **HeatLossJS**[^2] which implements a physics simulation for heat loss in a building. This is likely similar to the software Octopus would have used when they surveyed my house.

You enter lots of details about room sizes, radiators, building insulation, and then get answers about how much heat power (now we're back to kW again) you'll need. It's very comprehensive:

![alt text](heatlossjs.png)

You can see though, this is very easy to get _wrong_. If you put in the wrong insulation ratings "U-value", either by using an assumed Victorian-era figure (Octopus!) or by guessing from UK building regulations (me) then the numbers will be all over the place.

Here's why it's important:

| | Older house | Modern house |
|-|-|-|
| Insulation value (lower is better) | 2.4 | 0.2 |
| Heat power needed (yearly average) | 4 kW | 0.36 kW |
| Yearly heating cost | £2,400 | £200 |

[^1]:https://www.nao.org.uk/reports/decarbonising-home-heating/?nab=0
[^2]:https://github.com/trystanlea/heatlossjs
