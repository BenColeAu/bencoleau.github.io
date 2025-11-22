---
title: "Virtual Machine Autopilot"
date: 2025-11-21
draft: false
description: "Windows 11 Intune managed virtual Machine"
tags: ["Autopilot", "Intune"]
---
# Configure Virtual Machines with Autopilot - Windows 11

This week I pieced together a test Virtual Machine on VMWare.
Microsoft have several limitations that can be found posted on [MS Learn](https://learn.microsoft.com/en-us/intune/intune-service/fundamentals/windows-10-virtual-machines).

Let's go through each of these limitations and dive in a bit more about the specifics of them. 
After that I will take you through my build process and learnings so far.

### Intune does not support using a cloned image of a computer that is already enrolled.
This is fairly straight forward the hash used by devices is unique and when a new VM is created it generates a new hash, not much here and pretty easy to navigate. 
My recommendation capture your snapshot after installing Windows 11, then enroll your devices. This could probably be easily automated and then let OOBE do the rest.

### Windows Autopilot Self-deploying and pre-provisioning deployment types aren't supported because they require a physical Trusted Platform Module (TPM).
Let's break this up a little bit, the Self-deploying and pre-provisioning deployment follow much the same path. 
This fails straight away as the TPM cannot pass validation due to the lack of credentials, this is intentional by Microsoft as it posses a pretty serious security risk.
Thank you for the wisdom from the WinAdmins discord who explained this to me in more detail.
So the physical TPM module is quite literal.

### We recommend that you don't use Intune to manage on-demand, session-host virtual machines, also known as non-persistent virtual desktop infrastructure (VDI).

Out of Box Experience (OOBE) enrollment isn't supported on non-persistent VMs that can only be accessed by using RDP (such as VMs that are hosted on Azure). This restriction means:
Windows Autopilot and Commercial OOBE aren't supported.
Enrollment Status Page isn't supported.

These limitations/requirements are all related and tie back to the cloned image. Each time a new VM is spun up that Hash is going to change and your intune envrionment will start to be populated with bad data of devices that no longer exist.
If your in an envrionment like me, you may have already seen this occuring a bit when devices have synced across from SCCM.

## Virtual Machine Build.

So what did I end up doing?
For me it was a requirement to have these VMs shared across multiple users, which also means I needed the device to be recognised as shared and not hold a primary user.
In our envrionment we use the primary user as a reporting data source for our ITSM and Endpoint asset management so this was key to keep that data accurate.

The steps I followed are as follows:
1. Spin up the Virutal Machine.
2. Install Windows 11 25H2.
3. Take a snapshot of the VM, incase we need to fallback or build another later on.
4. Begin the hash process using the Get-WindowsAutopilotInfo Script with the -Online -Assign parameters.
5. Start the Autopilot using User-Drive deployment and let it complete. (More on the intune Configuration Later)
6. Once the device has completed I then removed the primary user and Synced the VM.

Removing the Primary user forces the machine into shared mode, any user logging in after this will be able to install apps and the machine will not revert back and assign a user. So this was a quick and easy Win.

## Intune Configuration

In my envrionment I use a series of Dynamic device entra groups and assign them to different profiles, my default profile simply looks for any device with a Hash.
Each time I create a new SOE build I add to this rule and exclude based on group tag. If I was to redo this in the future I would probably change this as each build means I need to revisit this rule and change it, which can be quite tempremental.

Once the Entra group is created I begin the Windows > Enrollment setup creating an ESP (Enrollment Status Page), Deployment Profile and assign them to the group.
Assign them to the group and away you go.
