---
layout: blog
title: Enable Copy and Paste with VMware Remote Console (VMRC)
date: 2023-04-24T01:24:28.866Z
img: copypaste.jpg
tags:
  - vmware
  - infrastructure
---




It has always been annoying that copy and paste is disabled by default in the VMware remote console. It is a good security setting but I never found it an easy setting to change. I've tried in the past but never got it to work properly. This last week I *finally* figured it out. It turns out reading is hard.

If we look at the official documentation it shows us the settings that we need to add to a virtual machine's advanced settings. My problem is, in my haste to make this work, I always thought these were the settings needed to *enable* copy and paste, when, in fact, these are the settings to *disable* copy and paste. This is an odd way to write the article because copying and pasting are disabled by default.

![VM advanced copy paste settings](\img\vm-adv-settings-copy-paste.png)

So, with my reading comprehension fully activated, we need to set those advanced settings but invert the values. Doing this manually is slow so I looked up the PowerCLI commands to make the change for a specific virtual machine. If you had a list of virtual machines that you wanted to change, these settings could be run through a foreach loop also.

`````powershell
## Declare a virtual machine and set the advanced settings that enable copy and paste through the VMware Remote Console
$vm = "vm01"
Get-AdvancedSetting -Entity $vm -Name isolation.tools.copy.disable | set-advancedsetting -value false -confirm:$false
Get-AdvancedSetting -Entity $vm -Name isolation.tools.paste.disable | set-advancedsetting -value false -confirm:$false
Get-AdvancedSetting -Entity $vm -Name isolation.tools.setGUIOptions.enable | set-advancedsetting -value true -confirm:$false
`````

I ran this on a virtual machine and I thought originally that it did not work as copy and paste was still not working. It turns out that this only works on the VMware Remote Console (installed console) and not the web console found in the VMware vSphere Web Client. 

Source: [Disable Copy and Paste Operations Between Guest Operating System and Remote Console (vmware.com)](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-367D02C1-B71F-4AC3-AA05-85033136A667.html) 