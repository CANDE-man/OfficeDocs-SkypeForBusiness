---
title: Allow meeting and webinar organizers to hide the names of attendees (Preview)
author: wlibebe
ms.author: wlibebe
manager: pamgreen
audience: Admin
ms.topic: article
ms.service: msteams
ms.reviewer: xonatia.lee
ms.date: 11/17/2023
ms.localizationpriority: medium
ms.collection: 
  - M365-collaboration
description: Learn how to allow meeting and webinar organizers to hide the names of attendees in Microsoft Teams meetings and webinars so names of attendees are hidden from other attendees in meeting stage, roster, and chat. 
appliesto: 
  - Microsoft Teams
---
# Allow meeting and webinar organizers to hide the names of attendees (Preview)

**APPLIES TO:** ✔️Meetings ✔️Webinars ✖️Town halls

[!INCLUDE[Teams Premium](includes/teams-premium-ecm.md)]

## Overview

With a Teams Premium license, you, as an admin can decide if meeting and webinar organizers can hide the names and photos of attendees from other attendees in the stage, roster, and chat.

To learn more about how your organizers can hide attendee names, see [Hide attendee names in Teams meetings and webinars](https://support.microsoft.com/office/hide-attendee-names-in-teams-meetings-and-webinars-00389c74-ee61-48b5-bad8-8295600085ed).

## Allow meeting and webinar organizers to hide the names of attendees through PowerShell

Through PowerShell, you can manage whether meeting and webinar organizers with a Premium license can hide the names of attendees.
The **`-AttendeeIdentityMasking`** parameter in the **CsTeamsMeetingPolicy** cmdlet controls whether your users can hide attendee names in their meetings and webinars.
The following table shows the behaviors of the settings for the **`-AttendeeIdentityMasking`** parameter:

|Setting value| Behavior|
|---------|---------------|
|Enabled| For organizers with this policy, attendee names are always hidden in their meetings and webinars.|
|DisabledUserOverride| **This is the default value.** Attendee names are visible, but meeting and webinar organizers can choose to hide attendee names in their meetings and webinars through their meeting options.|
|Disabled| For organizers with this policy, attendee names are always shown in their meetings and webinars.|

To enable **`-AttendeeIdentityMasking`**, so attendee names are always hidden in meetings and webinars that organizers with this policy create, run the following script:

```PowerShell
Set-CsTeamsMeetingPolicy -Identity <policy name> -AttendeeIdentityMasking Enabled
```

To disable **`-AttendeeIdentityMasking`** so attendee names are always shown in meetings and webinars organizers with this policy create, run the following script:

```PowerShell
Set-CsTeamsMeetingPolicy -Identity <policy name> -AttendeeIdentityMasking Disabled
```

To learn more about **`-AttendeeIdentityMasking`**, see the following cmdlet topics:

- [Set-CsTeamsMeetingPolicy](/powershell/module/skype/set-csteamseventspolicy)
- [New-CsTeamsMeetingPolicy](/powershell/module/skype/new-csteamseventspolicy)
- [Grant-CsTeamsMeetingPolicy](/powershell/module/skype/grant-csteamseventspolicy)
- [Get-CsTeamsMeetingPolicy](/powershell/module/skype/get-csteamseventspolicy)
- [Remove-CsTeamsMeetingPolicy](/powershell/module/skype/remove-csteamseventspolicy)

## Related articles

- [Set up webinars](set-up-webinars.md)
