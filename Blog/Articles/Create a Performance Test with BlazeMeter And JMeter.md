---
title: Create a Performance Test with BlazeMeter and JMeter
author: Peter Gregg
description: Create a Performance Test with BlazeMeter and JMeter
image: https://dummyimage.com/800x600/000/fff&text=placeholder
thumbnail: https://dummyimage.com/200x200/000/fff&text=placeholder
type: article
status: draft
published: 2022/06/20 08:00:00
categories: 
  - Performance
  - BlazeMeter
  - JMeter
  - Command Line
---


# Create a Performance Test with BlazeMeter and JMeter

In this article, you will learn how to create a performance test with BlazeMeter and JMeter.

## Prerequisites

The following prerequisites will be required to complete this tutorial:
- BlazeMeter browser extension. If you don't already have it,   [add the BlazeMeter extension to your browser](https://chrome.google.com/webstore/detail/blazemeter-the-continuous/mbopgmdnpcbohhpnfglgohlbhfongabi). 
- A BlazeMeter account. If you don't have a BlazeMeter account,  [create one for free](https://auth.blazemeter.com/auth/realms/blazect/login-actions/registration?client_id=blazemeter&tab_id=7GOVEWaplBc) before you begin.
- JMeter. If you don't have JMeter installed, [download JMeter](https://jmeter.apache.org/download_jmeter.cgi). 

## Create a performance test script using BlazeMeter

This section details how to create a performance test script using BlazeMeters browser extension.

1. Select the 
![BlazeMeter logo](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/BlazeMeterLogo.png) **Icon** in your browser.
2. In the **Test name** box, enter a name for the test. Select the **Record** button, to start recording. 

    ![BlazeMeter create test](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/BlazeMeterCreateTest.png)

3. Select the **Add Step** button, to add steps for each action. When finished, select the **Stop** button.

    ![BlazeMeter add step](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/BlazeMeterCreateTestSteps.png)

> [!NOTE] 
> When the script is saving the **BlazeMeter icon** will blink. 

4. Select **Edit**, and then **JMeter Script...** (If not logged in to BlazeMeter, you will need to be for the next steps).

    ![BlazeMeter edit JMeter script](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/BlazeMeterExportJMeterScriptStep1.png)
5. Select **JMeter & Selenium combined (YAML)**, and then select **Save**.

    ![BlazeMeter JMeter & selenium combined](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/BlazeMeterExportJMeterScriptStep2.png)

6. Select the **.JMX** button to export the JMeter script.

    ![BlazeMeter export a JMeter script](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/BlazeMeterExportJMeterScriptStep3.png)

## Open the performance test in JMeter

This section details how to run a performance test script in JMeter that was created and exported in BlazeMeter.

1. In Microsoft Windows, **Open** the bin folder of JMeter and run the **JMeter.bat** batch file.

    ![JMeter run on windows](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/JMeterRunOnWindows.png)

2. In the upper-left of the screen, select **File** > **Open**.

    ![JMeter open test script via file menu step 1](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/JMeterOpenBlazeMeterJMeterScriptStep1.png)

3. On the **Open** screen, select the **Script**, and then select **Open**. 

    ![JMeter open test script via file menu step 2](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/JMeterOpenBlazeMeterJMeterScriptStep2.png)

### Edit Thread Group properties
1. In the **Test Plan**, select the **Thread Group**. This is where you enter the essential information required for the performance test. Enter the following values:

    ![JMeter Thread Group](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/JMeterBlazeMeterThreadGroup.png) 

    | Field | Value | Description |
    | --- | --- | --- |
    | Name | 5 Users | Name of the Thread Group |
    | Comments |  | Comment related to the Thread Group |
    | Action to be taken after a Sampler error | Continue | Continue with the test when an error occurs |
    | Number of Threads (users) | 5 | Mimics the number of user(s) or connection(s) to an application |
    | Ramp-up period (seconds) | 10 | Time to create all of the threads (or how frequent requests should be sent by users) |
    | Loop Count | 1 | Number of times the test will repeat for the thread group (or quantity of requests that should be sent) |
    | Same user on each iteration | Checked | If checked and the Test Plan utilises HTTP Cookie Manager - Cookies returned in the first response for subsequent requests will be used |
    | Delay Thread Creation until needed | Unchecked | If checked it stops JMeter allocating memory for all threads (users) when the performance test starts |
    | Specify Thread lifetime | Unchecked | Specify a duration and startup delay |
    | Duration (seconds) | 0 | Specify a duration for the whole test |
    | Startup delay (seconds) | 0 | Delay time before a thread is started |

### Add listeners 

1. In the **Test Plan**, right-click the **Thread Group**, select **Add** >  **Listener** > **Summary Report**.

    ![JMeter add listener](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/JMeterThreadGroupAddListener.png) 

2. In the **Test Plan** follow step 4 above to add the following listeners to the  **Thread group** and each **Transaction controller**.

    | Test Plan Area | Listener | 
    | --- | --- | 
    | Thread Group | Summary Report | 
    | Thread Group | View Results Tree | 
    | Thread Group | Response Time Graph | 
    | Transaction Controller | Summary Report | 
    | Transaction Controller | View Results in Table | 
    | Transaction Controller | Aggregate Report | 
    | Transaction Controller | Graph Results | 

    ![JMeter add listeners](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/JMeterThreadGroupAddListeners.png) 

### Run the performance test and analyse results

1. In the JMeter screen, select the ![JMeter Start button](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/JMeterGUIStartPerformanceTest.png) button.

    Once running, a green indicator icon will appear in the top right of the Control Panel. JMeter will ramp up the total number of active threads to 5 in approximately 10 seconds with a delay of 2 secs between subsequent threads. The indicator box will turn to grey when the test is complete. 

2. When the performance test has completed. Select the **Summary Report** listener in the **Thread group** to analyse the results.

    ![JMeter summary report](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/JMeterThreadGroupSummaryReport.png)


























