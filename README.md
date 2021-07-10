# ThinkPad-X201-Arrandale-macOS-OpenCore
OpenCore bootloader configuration which allows tu run macOS on ThinkPad X201 with Arrandale CPU and 1st Generation Intel HD graphics (possibly on the t410, t510 and other Arrandale CPU machines too).

<img src="/images/ThinkPad-X201-macOS.jpg" alt="ThinkPad-X201-macOS" height="70%" width="70%">
<img src="/images/ThinkPad-X201-macOS-geekbench.png" alt="ThinkPad-X201-macOS-geekbench" height="70%" width="70%">



# What's working ✅
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

2) Enter your own PlatformInfo information (generatede for MacBookPro11,1 which is supported in BigSur) in ```EFI_for_installation/OC/config.plist``` and ```EFI_after_IntelHD_install/OC/config.plist```. Edit config.plist with [ProperTree](https://github.com/corpnewt/ProperTree) and change the following fields:
    ```
    PlatformInfo -> Generic -> MLB
    PlatformInfo -> Generic -> ROM
    PlatformInfo -> Generic -> SystemSerialNumber
    PlatformInfo -> Generic -> SystemUUID
    ```
    Generate new serials with [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS). The ROM value is your ethernet (en0) mac address ([more info](https://dortania.github.io/OpenCore-Post-Install/universal/iservices.html#fixing-en0)).


3) Create installation media as is described in [https://dortania.github.io/OpenCore-Install-Guide/installer-guide/](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/). 
For ThinkPad X201 which not supporting EFI boot there is need to create legacy setup described [https://dortania.github.io/OpenCore-Install-Guide/installer-guide/mac-install.html#legacy-setup](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/mac-install.html#legacy-setup)

4) Mount EFI partition from installer media [https://dortania.github.io/OpenCore-Install-Guide/installer-guide/mac-install.html#setting-up-opencore-s-efi-environment](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/mac-install.html#setting-up-opencore-s-efi-environment)

5) Copy EFI_for_installation directory to mounted EFI partation, rename EFI_for_installation directory to EFI.

6) Boot machine from your installation media and intall macOS.

    WARNING:

    * built-in keyboard not working in OpenCore picker - Currently I don't know how to fix that. For installation process I use USB keyboard.
    * Graphics in install process is really really slow (graphics QE/CI acceleration will be enabled in next step)
    * For some reason x201 unable to reboots himself. When intallator hangs when trying reboot, just power off machine (long pressing power button), and power on again. Several reboots are required to install the system. This looks as follows:
        - start installer (Install MacOS BigSur)
        - reboot, need power off and power on
        - start installer (MacOS installer)
        - reboot, need power off and power on
        - start installer (MacOS installer)
        - reboot, need power off and power on
        - start BigSur
        - reboot, need power off and power on
        - start BigSur (installation is complete)

# Getting graphics QE/CI acceleration work.
Graphics acceleration can be achieved by loading patched kext, thanks to ASentientBot for making it.

To load patched kext I use OpenCore Legacy Patcher dedicated for old Macs. Fortunately with small modification it can be used to load patched kexts for X201.

## How to load patched graphics kexts:
1) Install Xcode Command Line Tools. Open terminal and enter: ```xcode-select —install```.
2) Download OpenCore Legacy Patcher. I used version [0.1.7](https://github.com/dortania/OpenCore-Legacy-Patcher/archive/refs/tags/0.1.7.zip) and unzip it.
3) Comment lines 284 to 290 in Resources/SysPatch.py file. They should look as follows:
    ```
            # Misc patches
            # if self.brightness_legacy is True:
            #     print("- Installing legacy Brightness Control")
            #     self.add_brightness_patch()

            # if self.legacy_audio is True:
            #     print("- Fixing Volume Control Support")
            #     self.add_audio_patch()
    ```
    Above lines are required for old Macs, but not for ThinkPad X201.
4) Run OpenCore-Patcher.command from main directory.
    + select 4 (Change Model) and enter ```MacBookPro6,2```
    + select 3 (Post-Install Volume Patch)
    + power off your ThinkPad
5) Replace content of EFI directory on EFI partition with files from ```EFI_after_IntelHD_install```
6) Power on computer - graphics QE/CI acceleration should working now :) macOS on my ThinkPad X201 is running really fast and is pretty usable for web browsing, programming and other tasks :) 




