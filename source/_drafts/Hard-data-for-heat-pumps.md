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

![Picture of a smart heating thermostat on a wall](thermostat.png)

I'm a big advocate of STEM for getting people excited about the world around them, by bringing different skills together to solve interesting problems. In our case:

- **S**cience. Mostly some basic physics, like the fact that heat moves from a hot place to a colder one. And this happens faster when the temperature difference is greater - so you lose more heat from the house when it's colder outside. This is intuitively true, right? For your gas bill, cold days cost more.

- **T**echnology. We can use readily-available home automation tech to record the room temperatures.
- **E**ngineering. In this case, software engineering. We can write some computer programs to collect and process the data, and run simulations on them.
- **M**aths. Maths is everywhere! We'll use it for our simulations.

This is what we're going to do:

![Diagram showing approach to models](heat_pump_simple_diagram.svg)


{% mermaid %}
%%{init: {'flowchart': {'curve': 'linear', 'nodeSpacing': 80, 'rankSpacing': 100}, 'theme': 'base', 'themeVariables': {'primaryColor': '#E8F4F8', 'primaryBorderColor': '#0066CC', 'primaryTextColor': '#000', 'lineColor': '#0066CC', 'tertiaryColor': '#FFE6CC', 'tertiaryBorderColor': '#FF6600', 'tertiaryTextColor': '#000'}}}%%
flowchart TD
    subgraph theory["<b>THEORETICAL MODEL</b><br/>Physics-based"]
        T1["HeatLossJS<br/>Physics Simulation"]
        T2["Initial Parameters<br/>Room size, insulation,<br/>radiators, U-values"]
        T3["Model Predictions<br/>Heat power required<br/>per room"]
        T2 -->|feeds| T1
        T1 -->|outputs| T3
    end
    
    subgraph empirical["<b>EMPIRICAL MODEL</b><br/>Data-driven"]
        E1["4 Years of<br/>House Data"]
        E2["Smart Meters & Sensors<br/>Gas usage, room temps,<br/>radiator valve positions"]
        E3["Model Fitting<br/>Curve-fit thousands<br/>of data points"]
        E4["Measured Heat Loss<br/>Real-world performance<br/>per room"]
        E1 -->|collected via| E2
        E2 -->|analyzed by| E3
        E3 -->|produces| E4
    end
    
    subgraph refinement["<b>PARAMETER REFINEMENT</b>"]
        R1["Iterative Algorithm"]
        R2["Adjust theoretical<br/>parameters until<br/>predictions match<br/>empirical data"]
        R3["Calibrated Model<br/>Physics-based + Data-grounded"]
        R1 -->|applies| R2
        R2 -->|converges to| R3
    end
    
    T3 -->|compare| R1
    E4 -->|compare| R1
    
    R3 -->|reliable simulations| FINAL["Heat Pump<br/>Sizing"]
    
    style theory fill:#E8F4F8,stroke:#0066CC,stroke-width:2px,color:#000
    style empirical fill:#FFE6CC,stroke:#FF6600,stroke-width:2px,color:#000
    style refinement fill:#F0F0F0,stroke:#666,stroke-width:2px,color:#000
    style FINAL fill:#C8E6C9,stroke:#4CAF50,stroke-width:3px,color:#000
{% endmermaid %}

# Physics: the theoretical model

I found an amazing open-source project called **HeatLossJS**[^2] which implements a physics simulation for heat loss in a building. This is likely similar to the software Octopus would have used when they surveyed my house.

You enter lots of details about room sizes, radiators, building insulation, and then get answers about how much heat power (now we're back to kW again) you'll need. It's very comprehensive:

![HeatLossJS web application showing parameters and heat calculation](heatlossjs.png)

You can see though, this is very easy to get _wrong_. If you put in the wrong insulation ratings "U-value", either by using an assumed Victorian-era figure (Octopus!) or by guessing from UK building regulations (me) then the numbers will be all over the place.

Here's why it's important:

| | Older house | Modern house |
|-|-|-|
| Insulation value (lower is better) | 2.4 | 0.2 |
| Heat power needed (yearly average) | 4 kW | 0.36 kW |
| Yearly heating cost | £2,400 | £200 |

How can we make sure our parameters are right? Well, we can take measurements of the real heat performance of the house, and use those to "tune" the physics model until it matches reality. This is called developing an _empirical_ model. Let's do that.

# Technology: the empirical model

To find out how heat is being gained and lost for each room in the house, we need to take some measurements. I have some smart home tech that's useful here:

- Gas smart-meter. I have a smart In-Home Display from Hildebrand[^3] that sends a gas usage reading to my Home Assistant software every 30 seconds. This tells me the whole gas use for the house, but I need it calculated per-room. Which leads on to...
- Radiator smart valves. I can monitor how much hot water is being let into each radiator, from 0% to 100%, because they have smart TRVs from Tado[^4] which are integrated into Home Assistant.
- Room smart thermostats. Each room has wall-mounted thermostats, also Tado, which means that Home Assistant is recording the room temperatures. This is needed for telling how fast the rooms heat up. But also, less obviously, calculating the heat produced (i.e. gas used) by radiators needs the temperature difference between the radiator and the room's current air temperature.

Claude knocked up a diagram. Feel free to skip ahead if you're not interested in it!

![Diagram showing how smart home readings are combined](heat_input_calculation_diagram.svg)

# Engineering (software)

To bring this all together, I've built a computer program that has:

- A theoretical model, based on the open-source HeatLossJS project.
- An empirical model, which takes many thousands of readings from the house and "fits" a mathematical model to them.
- An algorithm that iteratively tweaks the theoretical model parameters until it matches the empirical model.
- And for fun, a feature enabling me to control the whole setup using Claude AI.

And what's the output? Well, I now have a well-calibrated theoretical model where I can confidently simulate what size of heat pump would be required for keeping my house at a comfortable temperature.



[^1]:https://www.nao.org.uk/reports/decarbonising-home-heating/?nab=0
[^2]:https://github.com/trystanlea/heatlossjs
[^3]:https://www.hildebrand.co.uk/products/ihd-cad
[^4]:https://www.tado.com/en-gb/products/radiators
