---
title: Monitor Microsoft Teams Rooms devices with Azure Monitor
ms.author: tonysmit
author: tonysmit
ms.reviewer: tjaved
ms.date: 11/02/2023
manager: pamgreen
audience: ITPro
ms.topic: article
ms.service: msteams
ms.subservice: itpro-rooms
f1.keywords: 
  - NOCSH
ms.localizationpriority: medium
ms.assetid: f8109905-3279-475f-a64b-31d37af48bfe
ms.collection: 
  - M365-collaboration
  - teams-rooms-devices
  - Tier1
description: This article discusses how to monitor Microsoft Teams Rooms devices in an integrated manner using Azure Monitor.
ms.custom: seo-marvel-apr2020
---

# Monitor Microsoft Teams Rooms devices with Azure Monitor

> [!IMPORTANT]
> This feature is being deprecated and being replaced by functionality found in the Teams admin center and Teams Rooms Pro Management portal. See [Overview of the Teams Rooms Pro Management portal](../rooms/rooms-pro-management.md) for more information.

This article discusses how to monitor Microsoft Teams Rooms in an integrated manner using Azure Monitor.

[!INCLUDE [teams-pro-license-requirement](../includes/teams-pro-license-requirement.md)]

You can configure Azure Monitor to provide basic telemetry to help you monitor Microsoft Teams meeting rooms devices. See [Plan Microsoft Teams Rooms management with Azure Monitor](azure-monitor-plan.md) and [Deploy Microsoft Teams Rooms management with Azure Monitor](azure-monitor-deploy.md) for details. As your monitoring solution matures, you can use other data and monitoring capabilities to create a more detailed view of device performance.

## Understand the log entries

The following event descriptions are inserted into the event log description field every five minutes, when the Microsoft Teams Rooms app records the corresponding information in the Windows event log. The Microsoft Monitoring Agent passes these records to Log Analytics in Azure Monitor, which parses them into the dashboard you created in [Deploy Microsoft Teams Rooms monitoring with Azure Monitor](azure-monitor-deploy.md). With the dashboard, you can look at individual log entries to determine courses of action if needed.

Event IDs 2000 and 3000 indicate that Teams Rooms is working as expected. Event IDs 2001 and 3001 indicate that an issue was found. Event ID 4000 may require action if seen more often than you expect, compared to a baseline you set or to other deployed devices in your organization.

Understanding these event descriptions alerts you to problems quickly, and provides a starting point for further troubleshooting.

| Event&nbsp;ID&nbsp;level|Event&nbsp;behavior&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|Event&nbsp;Description&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
|:---    |:---   |:---  |
| 2000  <br> Information | This is a healthy heartbeat event. Every 5 minutes, Microsoft Teams Rooms checks that it's signed in to Microsoft Teams or Skype for Business and has network and Exchange connectivity. <br> If all three factors are true, it writes Event ID 2000 into the event log every 5 minutes until the device is offline or one or more of the conditions are no longer met. | `{"Description":"Heartbeat is healthy.", "ResourceState":"Healthy", "OperationName":"Heartbeat", "OperationResult":"Pass", "OS":"Windows 10", "OSVersion":"10.0.14393.693", "Alias":"alias<span></span>@contoso.com",  "DisplayName":"Display name", "AppVersion":"1.0.38.0", "IPv4Address":"10.10.10.10",  "IPv6Address":"IP v6 address"}` <br><br> In this example, all heartbeat conditions were met and the Microsoft Teams Rooms device was marked as healthy. If there were errors, the app would record Event ID 2001 instead. |
| 2001  <br> Error | This is an app error event. Every 5 minutes, Microsoft Teams Rooms checks that it's signed into Microsoft Teams or Skype for Business with network and Exchange connectivity. If one or more factors aren't true, it writes EventID 2001 into the event log every 5 minutes until the device is offline or all conditions are met once again.  | `{"Description":"Network status : Healthy. Exchange status : Connected. Sign in status: Unhealthy. Teams sign in status: Healthy.", "ResourceState":"Unhealthy", "OperationName":"Heartbeat", "OperationResult":"Fail", "OS":"Windows 10", "OSVersion":"10.0.14393.693", "Alias":"", "DisplayName":"Display Name", "AppVersion":"1.0.38.0", "IPv4Address":"10.10.10.10", "IPv6Address":"ip v6 address"}` <br><br>  In this example, Microsoft Teams Rooms determined that the network connection was healthy and the app was connected to Exchange, but the bolded portion indicates that the app isn't connected. This could be a configuration issue on the device or host.  <br> <br> The Network status shows as either Healthy or Unhealthy. If the status is unhealthy, you may have a network issue or the device may have been unplugged (but then you would probably also have Exchange and Microsoft Teams or Skype for Business errors).  <br><br> The Exchange Status shows as either Connected or one of the following: Disconnected, Connecting, AutodiscoveryError (the most commonly seen error), GeneralError, or ServerVersionNotSupported. If the status is Connecting, wait until the next health message is sent, for other errors refer the issue to an admin with experience in solving Exchange issues.  <br><br>  The _sign in status_/_Teams sign in status_ (indicating the app is signed in to either Skype for Business or Teams) shows as either _Healthy_ or _Unhealthy_. If the status is unhealthy, send a technician to investigate further. |
| 3000  <br> Information | This event verifies that a hardware check was run and found to be healthy. Every 5 minutes Microsoft Teams Rooms checks that configured hardware components such as front-of-room display, microphone, speaker, and camera are connected and functioning. If all components are healthy, it writes EventID 3000 into the event log. This event is written every 5 minutes unless there's an issue with a connected device.  <br> | `{"Description":"HardwareCheckEngine is healthy.",  "ResourceState":"Healthy", "OperationName":"HardwareCheckEngine",  "OperationResult":"Pass", "OS":"Windows 10",  "OSVersion":"10.0.14393.693", "Alias":"alias<span></span>@contoso.com", "DisplayName":"Display Name", "AppVersion":"1.0.38.0",  "IPv4Address":"10.10.10.10", "IPv6Address":"ip v6 address"}` <br><br> In this example, all hardware checks were passed. If there were errors,   the app would record Event ID 3001 instead. |
| 3001  <br> Error Event  | This is a hardware error event. The Microsoft Teams Rooms app has a process that checks the health of connected hardware components (front of room, microphone, speaker, camera) every 5 minutes. If one or more of the components are unhealthy, it writes EventID 3001 into the event log. This event is written every 5 minutes until the issue with the device is fixed.   | `{"Description":" Front of Room Display status : Unhealthy. Configured display count is 2. Real display count is 0. Conference Microphone status : Unhealthy. Conference Speaker status : Healthy. Default Speaker status : Healthy. Camera status : Healthy.", "ResourceState":"Unhealthy", "OperationName":"HardwareCheckEngine", "OperationResult":"Fail", "OS":"Windows 10", "OSVersion":"10.0.14393.1198", "Alias":"alias<span></span>@contoso.com", "DisplayName":"Yosemite conference room", "AppVersion":"2.0.58.0", "IPv4Address":"10.10.10.10", "IPv6Address":"IPv6Address", "IPv4Address2":"10.10.10.10"}` <br><br>  Hardware peripherals are shown as either Healthy or Unhealthy. <br> In this example, there are two _front-of-room_ displays configured, and currently neither of them is available. The _Conference Microphone status_ is _unhealthy_, which could have several possible causes. Since at least one resource didn't pass the check, the ResourceState is listed as Unhealthy. Send a technician to investigate further. |
| 4000  <br> Information  <br> | This is an App Restart event. Every time the app is restarted, it logs this event into the Windows event log.  <br> | `{"Description":"App restarts.", "ResourceState":"Healthy", "OperationName":"Restart", "OperationResult":"Pass", "OS":"Windows 10", "OSVersion":"10.0.14393.693", "Alias":"alias<span></span>@domain.com", "DisplayName":"Display Name", "AppVersion":"1.0.38.0", "IPv4Address":"10.10.10.10", "IPv6Address":"ip v6 address"}` <br><br> The app may restart for various reasons. Compare the restart frequency of devices in the same building and in different buildings. Keep in mind known issues like power fluctuations and failures, as this may provide clues to infrastructure problems.|

## Related articles
 

[Plan Microsoft Teams Rooms monitoring with Azure Monitor](azure-monitor-plan.md)

[Deploy Microsoft Teams Rooms monitoring with Azure Monitor](azure-monitor-deploy.md)
