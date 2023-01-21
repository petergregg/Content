---
title: Simulate Network Speeds using Fiddler
author: Peter Gregg
description: Simulate Network Speeds using Fiddler
image: https://dummyimage.com/800x600/000/fff&text=placeholder
thumbnail: https://dummyimage.com/200x200/000/fff&text=placeholder
type: article
status: draft
published: 2022/01/21 13:00:00
categories: 
  - Network
  - Performance
  - Fiddler
---

# Simulate Network Speeds using Fiddler

In this article, you will learn how to simulate network speeds using fiddler.

## Prerequisites

The following prerequisites will be required to complete this tutorial:
- Fiddler. If you don't have Fiddler installed, [download Fiddler](https://www.telerik.com/download/fiddler).

# Simulate Network Speeds using Default Settings

This section details how to enable simulation of network speeds using Fiddlers default network speed settings.

1. In the upper-left of the screen, select **Rules** > **Performance** > **Simulate Modem Speeds**.  

    ![Fiddler Enable Simulate Modem Speeds](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/FiddlerEnableSimulateModemSpeeds.png)

# Simulate Network Speeds using Custom Settings
This section details how to enable simulation of network speeds using Fiddlers default network speed settings.

1. In the upper-left of the screen, select **Rules** > **Customize Rules**. 
2. On the **Fiddler ScriptEditor** screen, scroll down to `if (m_SimulateModel)`. 

    ![Fiddler Enable Simulate Modem Speeds](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/FiddlerCustomiseSimulatedModemSpeeds.png)

3. Update the `request-trickle-delay` value and `response-trickle-delay` value to the network speed you desire. The following values is what I got on my network and can be used as a guide:

| Request Value | Response Value | Download (Mbps) | Upload (Mbps) |
| --- | --- | --- | --- | 
| 1 | 1 | 30.57 | 25.78 |
| 2 | 2 | 0.28 | 0.28 |
| 4 | 4 | 0.1 | 0.26 |
| 8 | 8 | 0.26 | 0.14 |
| 16 | 16 |0.28 | 0.28 |
| 32 | 32 |0.14 |0.12 |
| 64 | 64 | 0.09 | 0.58|
| 128 | 128 | 0.06 | 0.49 |
