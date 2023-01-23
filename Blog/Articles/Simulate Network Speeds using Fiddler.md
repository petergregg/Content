---
title: Simulate Network Speeds using Fiddler
author: Peter Gregg
description: Simulate Network Speeds using Fiddler
image: https://dummyimage.com/800x600/000/fff&text=placeholder
thumbnail: https://dummyimage.com/200x200/000/fff&text=placeholder
type: article
status: published
published: 2023/01/23 23:39:00
categories: 
  - Network
  - Performance
  - Fiddler
---

# Simulate Network Speeds using Fiddler

In this article, you will learn how to simulate network speeds using fiddler.

## Prerequisites

The following prerequisites will be required to complete this tutorial:
- Fiddler. If you don't have Fiddler installed, [download Fiddler for free](https://www.telerik.com/download/fiddler).

# Simulate Network Speeds using Default Settings

This section details how to enable simulation of network speeds using Fiddlers default network speed settings.

1. In the upper-left of the screen, select **Rules** > **Performance** > **Simulate Modem Speeds**.  

    ![Fiddler Enable Simulate Modem Speeds](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/FiddlerEnableSimulateModemSpeeds.png)

# Simulate Network Speeds using Custom Settings
This section details how to customize simulation of network speeds.

1. In the upper-left of the screen, select **Rules** > **Customize Rules**. 
2. On the **Fiddler ScriptEditor** screen, scroll down to `if (m_SimulateModel)`. 

    ![Fiddler Enable Simulate Modem Speeds](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/FiddlerCustomiseSimulatedModemSpeeds.png)

3. Update the `request-trickle-delay` value and `response-trickle-delay` value to the network speed you desire. I used the following values to simulate the following network speeds captured with [speedof.me](https://speedof.me/).

| Request Value | Response Value | Download (Mbps) | Upload (Mbps) | Latency (ms) |
| --- | --- | --- | --- | --- |
| 1 | 1 | 15.65 | 28.4 | 30 |
| 2 | 2 | 0.27 | 0.28 | 56 |
| 4 | 4 | 0.26 | 0.28 | 42 |
| 8 | 8 | 0.26 | 0.27 | 67 |
| 16 | 16 |0.26 | 0.26 | 2576 |
| 32 | 32 |0.11 |0.13 | 87 |
| 64 | 64 | 0.07 | 0.56| 137 |
| 128 | 128 | 0.05 | 0.42 | 2418 |

4. To enable the simulation of one of the above network speeds, complete steps 1 in [Simulate Network Speeds using Default Settings](#simulate-network-speeds-using-default-settings).
