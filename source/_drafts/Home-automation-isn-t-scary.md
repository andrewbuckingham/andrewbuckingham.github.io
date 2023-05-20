---
title: Home automation isn't scary
tags:
---

Not so long ago, the height of home automation was aspiring to a garage with a remote controlled door. Simpler times.

And then things got complex. Designer homes for the well-heeled, replete with extensive electronics to control lighting and home entertainment. Industrious individuals — *brave individuals* — would charge handsomly for installing these systems; taking on the risk when they inevitably didn't work quite as well as their client expected.

These days, anyone with even the slightest of tech abilities can automate virtually every electronic device in their household.

If you've not tried it yet, I'd like to persuade you it's time to start.

# Why bother?

The wonderful thing about home automation is there's no single answer to the *"why"*. It's so phenomenally broad that there'll always be a topic that appeals to your unique motives. Pick that as your own starting point. A few ideas:

* Mood lighting that changes depending on what movie you're watching
* Coffee pot that switches on when you're nearly home
* Home heating that comes on earlier if it's cold outside
* Watering your houseplants when you're away on holiday

# Start simple




```mermaid
    gitGraph
        branch "Home Assistant capabilities"
        commit id: "Raspberry Pi!"
        commit id: "Added Tado heating"
        commit id: "Added Node-RED"
        commit id: "Added watertank sensor"

        branch "House data intelligence" order: 3
        commit id: "Temp and humidity calcs"
        commit id: "Fancy charts"
        commit id: "Rate of change calcs"

        checkout "Home Assistant capabilities"

        commit id: " "
        branch "Water heating efficiency" order: 1
        merge "House data intelligence"
        commit id: "Detect showers on"
        commit id: "Heat hot water using Tado"

        checkout "Home Assistant capabilities"
        commit id: "Added Wifi Plugs"

        branch "Power efficiency" order: 2
        merge "House data intelligence"
        commit id: "Automated clothes drying"

        checkout "Home Assistant capabilities"
        commit id: "Added home/away detection"

        checkout "Power efficiency"
        merge "Home Assistant capabilities"
        commit id: "Automated coffee machine"
        commit id: "Automated Octopus Energy Saving"

        checkout "Home Assistant capabilities"
        commit id: "Added Solar PV Battery integration"

        branch "Solar PV efficiency" order: 4
        commit id: "Gather Solar PV Stats"
        commit id: "Predict energy use"

        checkout "Power efficiency"
        merge "Solar PV efficiency"
        commit id: "Turn on dishwasher when optimal"

        checkout "Solar PV efficiency"
        commit id: "Optimise overnight charging"

        checkout "Home Assistant capabilities"
        commit id: "Add voice control"
```