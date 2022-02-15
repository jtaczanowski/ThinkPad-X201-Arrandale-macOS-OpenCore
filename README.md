# ThinkPad-X201-Arrandale-macOS-OpenCore
OpenCore bootloader configuration which allows you to run macOS on ThinkPad X201 with Arrandale CPU and 1st Generation Intel HD graphics (possibly on the t410, t510 and other Arrandale CPU machines too).

<img src="/images/ThinkPad-X201-macOS.jpg" alt="ThinkPad-X201-macOS" height="70%" width="70%">
<img src="/images/ThinkPad-X201-macOS-geekbench.png" alt="ThinkPad-X201-macOS-geekbench" height="70%" width="70%">



# What's working - tested on macOS BigSur 11.6.4 ✅
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


# Installation procedure
1) Check your BIOS settings:
    * SATA controller set to AHCI
    * Display set to Thinkpad LCD
    * under CPU - Hyper-Threading is Enabled, virtualization and VT-d feature are both disabled.

2) Enter your own PlatformInfo information (generated for MacBookPro11,1 which is supported in BigSur) in ```EFI/OC/config.plist```Edit config.plist with [ProperTree](https://github.com/corpnewt/ProperTree) and change the following fields:
    ```
    PlatformInfo -> Generic -> MLB
    PlatformInfo -> Generic -> ROM
    PlatformInfo -> Generic -> SystemSerialNumber
    PlatformInfo -> Generic -> SystemUUID
    ```
    Generate new serials with [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS). The ROM value is your ethernet (en0) mac address ([more info](https://dortania.github.io/OpenCore-Post-Install/universal/iservices.html#fixing-en0)).


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
1) Download OpenCore Legacy Patcher. I used version [0.4.2](https://github.com/dortania/OpenCore-Legacy-Patcher/releases/download/0.4.2/OpenCore-Patcher-GUI.app.zip) and unzip it.
2) Run OpenCore-Patcher.
    + Select "Settings" (Change Model) and choose ```MacBookPro6,2```
    + Select "Non-Metal Settings" and pick "Enable Beta Blur"
    + Click "Return to Main Menu"
    + Run "Post Install Root Patch"
3) Power on computer - graphics QE/CI acceleration should working now :) macOS on my ThinkPad X201 is running really fast and is pretty usable for web browsing, programming and other tasks :) 

# Getting CPU Power Management (Intel SpeedStep) to work.
CPU Power Management. Thanks to the patched LPC device-id ```pci8086,3b07``` -> ```pci8086,3b09``` and setting SystemProductName to MacBookPro6,2 native power management through appleLPC is fully working like on original MacBookPro6,2. CPU speedstep is working, Turbo Boost is also working - my CPU clock exceeds 3 GHz when needed.

## How to enable CPU Power Management:
1) Edit your EFI/OC/config.plist and change SystemProductName to ```MacBookPro6,2```

# Getting macOS OTA updates to work
For SystemProductName in EFI/OC/config.plist set to ```MacBookPro6,2``` OTA updates will not work, because ```MacBookPro6,2``` is not supported by macOS BigSur originally. It is needed to change SystemProductName to actually supported model.

## How to update macOS through OTA:
1) Edit your EFI/OC/config.plist and change SystemProductName to ```MacBookPro11,1```
2) In system preferences check if are updates available and install it.
3) Install patched graphics kext if needed (# Getting graphics QE/CI acceleration work).
3) Restore SystemProductName in EFI/OC/config.plist to ```MacBookPro6,2``` to get CPU Power Management working again.

# Special thanks for cooperation, inspiration, great software and documentation to:
- [jamesfawcett](https://github.com/jamesfawcett)
- [Dortania](https://github.com/dortania)
- [Acidanthera](https://github.com/acidanthera)
- [OpenCore](https://github.com/OpenCorePkg)
- [ASentientBot](https://github.com/ASentientBot)
- [dosdude1](https://github.com/dosdude1)
