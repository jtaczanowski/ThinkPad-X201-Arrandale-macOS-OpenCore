# ThinkPad-X201-Arrandale-macOS-OpenCore
OpenCore bootloader configuration which allows you to run macOS on ThinkPad X201 with Arrandale CPU and 1st Generation Intel HD graphics (possibly on the t410, t510 and other Arrandale CPU machines too).

<img src="/images/ThinkPad-X201-macOS.jpg" alt="ThinkPad-X201-macOS" height="70%" width="70%">
<img src="/images/ThinkPad-X201-macOS-geekbench.png" alt="ThinkPad-X201-macOS-geekbench" height="70%" width="70%">



# What's working - tested on macOS BigSur 11.6.6 ✅
- [x] CPU Power Management. Thanks to the patched LPC device-id ```pci8086,3b07``` -> ```pci8086,3b09``` and setting SystemProductName to MacBookPro6,2 native power management through appleLPC is fully working like on oryginal MacBookPro6,2. CPU speedstep is working, Turbo Boost is also working - my CPU clock exceeds 3 GHz when needed.
- [x] Intel HD graphics with QE/CI acceleration
- [x] USB ports
- [x] Intel WiFi - thanks to AirportItlwm.kext
- [x] Bluetooth
- [x] Speakers, headphones
- [x] Internal Camera
- [x] Internal Microphone
- [x] Battery indicator
- [x] Trackpad is working (trackpoint sometimes works, sometimes not)
- [x] SD card reader

# What's not working ⚠️
- [ ] WWAN
- [ ] Sleep
- [ ] Rebooting
- [ ] VGA port

# Initial macOS Monterey support
With this OpenCore EFI it is possible to run macOS Monterey on ThinkPad x201 with some issues (internal camera and bluetooth are not working) - which maybe will resolved in the future.

## What's working - tested on macOS Monterey 12.3.1 ✅
- [x] CPU Power Management. Thanks to the patched LPC device-id ```pci8086,3b07``` -> ```pci8086,3b09``` and setting SystemProductName to MacBookPro6,2 native power management through appleLPC is fully working like on oryginal MacBookPro6,2. CPU speedstep is working, Turbo Boost is also working - my CPU clock exceeds 3 GHz when needed.
From macOS 12.3 Beta 1 an additional kext ```ASPP-Override.kext``` id needed. More information at this [link](https://github.com/dortania/OpenCore-Legacy-Patcher/blob/main/resources/build.py#L177)
- [x] Intel HD graphics with QE/CI acceleration
- [x] USB ports
- [x] Intel WiFi - thanks to AirportItlwm.kext
- [x] Speakers, headphones
- [x] Internal Microphone
- [x] Battery indicator
- [x] Trackpad is working (trackpoint sometimes works, sometimes not)
- [x] SD card reader

## What's not working ⚠️
- [ ] WWAN
- [ ] Bluetooth
- [ ] Sleep
- [ ] Rebooting
- [ ] VGA port
- [ ] Internal Camera


<img src="/images/ThinkPad-X201-macOS-Monterey.jpg" alt="ThinkPad-X201-macOS-Monterey" height="70%" width="70%">

# Installation procedure
1) Check your BIOS settings:
    * SATA controller set to AHCI
    * Display set to Thinkpad LCD
    * under CPU - Hyper-Threading is Enabled, virtualization and VT-d feature are both disabled.

2) This step is optionally. You can use values provided in this repo. But if you would like to login to your apple accounts like icloud, imessage, facetime it is recommended to generate and enter your own PlatformInfo.

    Edit config.plist (```EFI/OC/config.plist```) with [ProperTree](https://github.com/corpnewt/ProperTree) and change the following fields:
    ```
    PlatformInfo -> Generic -> MLB (Board Serial in GenSMBIOS)
    PlatformInfo -> Generic -> ROM (info how to obtain below)
    PlatformInfo -> Generic -> SystemSerialNumber (Serial in GenSMBIOS)
    PlatformInfo -> Generic -> SystemUUID (SSmUUID in GenSMBIOS)
    ```
    Enter your own PlatformInfo information (generated for MacBookPro6,2) with [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS). The ROM value is your ethernet (en0) mac address ([more info](https://dortania.github.io/OpenCore-Post-Install/universal/iservices.html#fixing-en0)).


3) Create installation media as is described in [https://dortania.github.io/OpenCore-Install-Guide/installer-guide/](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/). 
For ThinkPad X201 which doesn't support EFI boot you have to create a legacy setup described here [https://dortania.github.io/OpenCore-Install-Guide/installer-guide/mac-install.html#legacy-setup](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/mac-install.html#legacy-setup)

4) Mount EFI partition from installer media [https://dortania.github.io/OpenCore-Install-Guide/installer-guide/mac-install.html#setting-up-opencore-s-efi-environment](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/mac-install.html#setting-up-opencore-s-efi-environment)

5) Copy EFI directory to mounted EFI partation.

6) Boot machine from your installation media and intall macOS.

    WARNING:

    * built-in keyboard not working in OpenCore picker - Currently I don't know how to fix that. For installation process I use a USB keyboard.
    * Graphics in install process is really slow (graphics QE/CI acceleration will be enabled in next step)
    * For some reason, the X201 isn't able to reboot automatically. When installer hangs when trying to reboot, just power off the machine (long pressing power button), and power on again. Several reboots are required to install the system. This looks as follows:
        - start installer (Install macOS BigSur)
        - reboot, need power off and power on
        - start installer (macOS installer)
        - reboot, need power off and power on
        - start installer (macOS installer)
        - reboot, need power off and power on
        - start BigSur
        - reboot, need power off and power on
        - start BigSur (installation is complete)

# Getting graphics QE/CI acceleration to work.
Graphics acceleration can be achieved by loading patched kext, thanks to ASentientBot for making it.

To load patched kext I use OpenCore Legacy Patcher dedicated for old Macs. Fortunately it can be used to load patched kexts for X201.

## How to load patched graphics kexts:
1) Download OpenCore Legacy Patcher. I used version [0.4.5](https://github.com/dortania/OpenCore-Legacy-Patcher/releases/download/0.4.5/OpenCore-Patcher-GUI.app.zip) and unzip it.
2) Run OpenCore-Patcher.
    + Select "Settings" (Change Model) and choose ```MacBookPro6,2```
    + Select "Non-Metal Settings" and pick "Enable Beta Blur"
    + Click "Return to Main Menu"
    + Run "Post Install Root Patch"
3) Power on computer - graphics QE/CI acceleration should working now :) macOS on my ThinkPad X201 is running really fast and is pretty usable for web browsing, programming and other tasks :) 

# How to update macOS through OTA:
1) In system preferences check if are updates available and install it.
2) Install patched graphics kext if needed (# Getting graphics QE/CI acceleration work).

# Information about CPU Power Management (Intel SpeedStep).
CPU Power Management. Thanks to the patched LPC device-id ```pci8086,3b07``` -> ```pci8086,3b09``` and setting SystemProductName to MacBookPro6,2 native power management through appleLPC is fully working like on original MacBookPro6,2. CPU speedstep is working, Turbo Boost is also working - my CPU clock exceeds 3 GHz when needed.

# Special thanks for cooperation, inspiration, great software and documentation to:
- [jamesfawcett](https://github.com/jamesfawcett)
- [Dortania](https://github.com/dortania)
- [Acidanthera](https://github.com/acidanthera)
- [OpenCore](https://github.com/OpenCorePkg)
- [ASentientBot](https://github.com/ASentientBot)
- [dosdude1](https://github.com/dosdude1)
