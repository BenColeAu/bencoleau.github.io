---
title: "Windows 11 Start Menu on Left"
date: 2025-11-16
draft: false
description: "a description"
tags: ["Windows 11", "Intune"]
---
I've made available the script that I use to position the start menu on the left side of the taskbar.
[Github](https://github.com/BenColeAu/Intune/tree/de1abf160e0b1ea2b1cea2241efdc30d1b3e491b/Start%20Menu%20Left)

Using the Intune Content Prep Tool, package the script and deploy as a Win32 application, ensure that you sign the script depending on your organsisations policies for powershell.
The application deployment has been tested in Autopilot OOBE, ensure this is assigned to a user group.
You can deploy this at the device level however the experience changes for shared devices. Due to the registery change being made modifies the HKCU.

This is lightweight change that you can make to ease the Windows 11 transition for your users, especially if you have older users.
The other option is to begin educating your users on the new start menu location. 

I'll make edits to this page if we get a new settings configuration from Microsoft to change the start menu location as this would be the most stable and preffered option.

Best of Luck,
Ben
