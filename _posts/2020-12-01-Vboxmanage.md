---
title: Virtualbox CLI - Getting started with vboxmanage
author: tactinix
date: 2020-12-01
categories: [Notes]
tags: [Virtualization]
math: true
mermaid: true
---

Virtualization by now certainly needs no introduction. As a Linux / nix user there are many solid options available. On Linux you have Libvirt, QEMU/Kvm, Gnome Boxes which uses the former as a base and lately Canonical's Multipass for Ubuntu appliances's. Most of the BSD's also have their own native solution, FreeBSD has BHYVE, OpenBSD has VMM etc. There is also VMware's range of products, Player (Free) and Workstation (Paid). 


#### Why Virtualbox?

Virtualbox, out of all these, has always been the most hassle free to install and get going with. A solid feather in it's cap and why I always return to it for my virtualiztion needs, is it's ability to bridge to wlp2s0/wlan0 (wifi). You see, my main workstation is a laptop, connected to either home or office wifi. I've never really taken to using NAT and prefer the VM's to have a "real" ip on my local network. Just makes more sense in my enviroment and allot simpler. VMware can also do it, but given the choice between Opensource and whatever else, Opensource wins, always.


#### vboxmanage - The CLI

Virtualbox comes with a perfectly good GUI, but when using tilling WM's on your desktop, it can become awkward if not configured in your WM's config. Besides, using the terminal is fun.

For the following I'll be using a VM from Vulnhub, but the usage remains the
same irrespective of whether you're importing a Bitnami or other VM from
another source.

The image in question is Photographer 1, found here {https://www.vulnhub.com/entry/photographer-1,519/}. The credit for building this VM goes to v1n1v131r4It {https://twitter.com/v1n1v131r4}. When downloading, it will
download as ```Photographer.ova```. OVA being the file format used by Virtualbox. Basically it's just a container format for housing the initial disk image and other configuration files.

Before I continue, the following asumes Virtualbox is installed by whatever means on a Linux host, the virtualbox kernel modules for the running kernel is loaded and the user account in use are in the ```vboxusers``` group. Also, this is certainly not a comprehensive tutorial in the workings of Virtualbox or```vboxmanage```, but rather aims to be just enough to get started with.

Also, I have the following alias sourced in my .zshrc, why type ```vboxmanage```
when ```vm``` will do.

```bash
alias vm='vboxmanage'
```


To import the VM image downloaded;

```bash
~ >>> vm import Photographer.ova
0%...10%...20%...30%...40%...50%...60%...70%...80%...90%...100%
Interpreting /home/tactinix/Photographer.ova...
OK.
0%...10%...20%...30%...40%...50%...60%...70%...80%...90%...100%
Disks:
  vmdisk1	536870912000	-1	http://www.vmware.com/interfaces/specifications/vmdk.html#streamOptimized	Photographer-disk001.vmdk-1	-1	

Virtual system 0:
 0: Suggested OS type: "Ubuntu_64"
    (change with "--vsys 0 --ostype <type>"; use "list ostypes" to list all possible values)
 1: Suggested VM name "Photographer"
    (change with "--vsys 0 --vmname <name>")
 2: Suggested VM group "/"
    (change with "--vsys 0 --group <group>")
 3: Suggested VM settings file name "/home/tactinix/VirtualBox VMs/Photographer/Photographer.vbox"
    (change with "--vsys 0 --settingsfile <filename>")
 4: Suggested VM base folder "/home/tactinix/VirtualBox VMs"
    (change with "--vsys 0 --basefolder <path>")
 5: Number of CPUs: 1
    (change with "--vsys 0 --cpus <n>")
 6: Guest memory: 1024 MB
    (change with "--vsys 0 --memory <MB>")
 7: Sound card (appliance expects "", can change on import)
    (disable with "--vsys 0 --unit 7 --ignore")
 8: USB controller
    (disable with "--vsys 0 --unit 8 --ignore")
 9: Network adapter: orig HostOnly, config 3, extra slot=0;type=HostOnly
10: CD-ROM
    (disable with "--vsys 0 --unit 10 --ignore")
11: IDE controller, type PIIX4
    (disable with "--vsys 0 --unit 11 --ignore")
12: IDE controller, type PIIX4
    (disable with "--vsys 0 --unit 12 --ignore")
13: SATA controller, type AHCI
    (disable with "--vsys 0 --unit 13 --ignore")
14: Hard disk image: source image=Photographer-disk001.vmdk, target path=Photographer-disk001.vmdk, controller=13;channel=0
    (change target path with "--vsys 0 --unit 14 --disk path";
    disable with "--vsys 0 --unit 14 --ignore")
Successfully imported the appliance.
~ >>>
```
If all went well with the import, you can then probe the appliance to make
sure all settings are appropriate to your host. As my preffrence are to run in bridged mode, I particularly look at what's up with NIC 1.


```bash
~ >>> vm showvminfo Photographer
Name:                        Photographer
Groups:                      /
Guest OS:                    Ubuntu (64-bit)
UUID:                        e366cea6-6420-471a-99c9-81645b5bb75a
Config file:                 /home/tactinix/VirtualBox VMs/Photographer/Photographer.vbox
Snapshot folder:             /home/tactinix/VirtualBox VMs/Photographer/Snapshots
Log folder:                  /home/tactinix/VirtualBox VMs/Photographer/Logs
Hardware UUID:               e366cea6-6420-471a-99c9-81645b5bb75a
Memory size                  1024MB
Page Fusion:                 disabled
VRAM size:                   16MB
CPU exec cap:                100%
HPET:                        disabled
CPUProfile:                  host
Chipset:                     piix3
Firmware:                    BIOS
Number of CPUs:              1
PAE:                         disabled
Long Mode:                   enabled
Triple Fault Reset:          disabled
APIC:                        enabled
X2APIC:                      enabled
Nested VT-x/AMD-V:           disabled
CPUID Portability Level:     0
CPUID overrides:             None
Boot menu mode:              message and menu
Boot Device 1:               Floppy
Boot Device 2:               DVD
Boot Device 3:               HardDisk
Boot Device 4:               Not Assigned
ACPI:                        enabled
IOAPIC:                      enabled
BIOS APIC mode:              APIC
Time offset:                 0ms
RTC:                         UTC
Hardware Virtualization:     enabled
Nested Paging:               enabled
Large Pages:                 disabled
VT-x VPID:                   enabled
VT-x Unrestricted Exec.:     enabled
Paravirt. Provider:          Default
Effective Paravirt. Prov.:   KVM
State:                       powered off (since 2020-07-21T09:45:28.000000000)
Graphics Controller:         VMSVGA
Monitor count:               1
3D Acceleration:             disabled
2D Video Acceleration:       disabled
Teleporter Enabled:          disabled
Teleporter Port:             0
Teleporter Address:
Teleporter Password:
Tracing Enabled:             disabled
Allow Tracing to Access VM:  disabled
Tracing Configuration:
Autostart Enabled:           disabled
Autostart Delay:             0
Default Frontend:
VM process priority:         default
Storage Controller Name (0):            IDE
Storage Controller Type (0):            PIIX4
Storage Controller Instance Number (0): 0
Storage Controller Max Port Count (0):  2
Storage Controller Port Count (0):      2
Storage Controller Bootable (0):        on
Storage Controller Name (1):            SATA
Storage Controller Type (1):            IntelAhci
Storage Controller Instance Number (1): 0
Storage Controller Max Port Count (1):  30
Storage Controller Port Count (1):      1
Storage Controller Bootable (1):        on
IDE (1, 0): Empty
SATA (0, 0): /home/tactinix/VirtualBox VMs/Photographer/Photographer-disk001.vmdk (UUID: 719489f8-f840-4363-9cd6-2cb8ac3f50c0)
NIC 1:                       MAC: 08002713A899, Attachment: Host-only Interface 'vboxnet0', Cable connected: on, Trace: off (file: none), Type: 82540EM, Reported speed: 0 Mbps, Boot priority: 0, Promisc Policy: deny, Bandwidth group: none
NIC 2:                       disabled
NIC 3:                       disabled
NIC 4:                       disabled
NIC 5:                       disabled
NIC 6:                       disabled
NIC 7:                       disabled
NIC 8:                       disabled
Pointing Device:             USB Tablet
Keyboard Device:             PS/2 Keyboard
UART 1:                      disabled
UART 2:                      disabled
UART 3:                      disabled
UART 4:                      disabled
LPT 1:                       disabled
LPT 2:                       disabled
Audio:                       enabled (Driver: PulseAudio, Controller: AC97, Codec: AD1980)
Audio playback:              enabled
Audio capture:               disabled
Clipboard Mode:              disabled
Drag and drop Mode:          disabled
VRDE:                        disabled
OHCI USB:                    enabled
EHCI USB:                    disabled
xHCI USB:                    disabled

USB Device Filters:

<none>

Bandwidth groups:  <none>

Shared folders:<none>

Capturing:                   not active
Capture audio:               not active
Capture screens:             0
Capture file:                /home/tactinix/VirtualBox VMs/Photographer/Photographer.webm
Capture dimensions:          1024x768
Capture rate:                512kbps
Capture FPS:                 25kbps
Capture options:

Guest:

Configured memory balloon size: 0MB


~ >>>
```
Running just  ```vm showvminfo $VMNAME```  gives a fairly comprehensive
overview. A common issue I have had with some VM's are the Graphics Controller settings, in this particular case, I'll end up running the appliance in headless mode, so not concerned with it now. Another setting to do a quick double check on would be allocated RAM. 

As I'm most interested in how the VM is networked, using ```grep```, one can
get to the good bits quick.

```bash
~ >>> vm showvminfo Photographer | grep 'NIC 1'
NIC 1:                       MAC: 08002713A899, Attachment: Host-only Interface 'vboxnet0', Cable connected: on, Trace: off (file: none), Type: 82540EM, Reported speed: 0 Mbps, Boot priority: 0, Promisc Policy: deny, Bandwidth group: none
~ >>>
```
As expected, the apliance are setup for Host-only networking on 'vboxnet0'.
I'll change that to Bridged on wlp2s0, my wifi addapter interface.

Firstly, tell Virtualbox that we want NIC 1 to be bridged; 
```bash
~ >>> vm modifyvm Photographer --nic1 bridged
```
Then to tell the freshly bridged adapter what interface to use;
```bash
~ >>> vm modifyvm Photographer --bridgeadapter1 wlp2s0
```

Now, a quick check to see if all went acording to plan;
```bash
~ >>> vm showvminfo Photographer | grep 'NIC 1'
NIC 1:                       MAC: 080027128E8F, Attachment: Bridged Interface 'wlp2s0', Cable connected: on, Trace: off (file: none), Type: 82540EM, Reported speed: 0 Mbps, Boot priority: 0, Promisc Policy: deny, Bandwidth group: none
```

Then fire it up;
```bash
~ >>> vm startvm Photographer --type headless
Waiting for VM "Photographer" to power on...
VM "Photographer" has been successfully started.
```
Doing an  ```nmap -sn```  on local reveals the assigned IP and we're ready to
go;
```bash
~ >>> nmap -sn 192.168.178.1/24
Starting Nmap 7.91 ( https://nmap.org ) at 2020-11-30 13:08 CAT
Nmap scan report for #####.### (192.168.178.1)
Host is up (0.029s latency).
[REDACTED]
Nmap scan report for photographer.#####.### (192.168.178.32)
Host is up (0.00028s latency).
[REDACTED]
Nmap done: 256 IP addresses (## hosts up) scanned in 4.17 seconds
```
One can also use  ```vboxmanage```  to get a list of running VM's;
```bash
~ >>> vm list runningvms
"Photographer" {17c8022c-3470-43b6-b0ae-e0f9ba79be53}
```
This can be expanded with the ```-l``` flag, but if you have a few VM's
running, this will output alot to the terminal.
To get detailed info of a specific running VM, ```showvminfo $VMNAME```
can be passed.

After utilizing the VM for whatwver it was meant to do, in this case getting
the root flag, the VM can be powered off with;

```bash
~ >>> vm controlvm Photographer poweroff
0%...10%...20%...30%...40%...50%...60%...70%...80%...90%...100%
```
And to delete the VM and reclaim resources;
```bash
~ >>> vm unregistervm Photographer --delete
0%...10%...20%...30%...40%...50%...60%...70%...80%...90%...100%
```

This does not even tickle the tippy tip of what ```vboxmanage``` can do but
should be enough to get anybody going with Virtualbox from the command line.

Thanks for reading and have the best day ever!

tactinix

