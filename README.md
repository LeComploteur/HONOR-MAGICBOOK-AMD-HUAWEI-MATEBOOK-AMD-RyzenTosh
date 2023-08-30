# RyzenTosh on Honor Magicbook Pro

## System Specs

* CPU: Ryzen 5 4600H
* GPU: AMD Radeon Vega 6
* RAM: 16 GB DDR4 (Soldered)
* Audio: TBD
* Panel: 16"
* SSD: 516 GB SN570 (replaced by 1TB Samsung 980)
* Wi-Fi: Realtek 8822CE Wireless LAN 802.11ac PCI-E NIC

## Working OSes

* Ventura (tested 13.4.1, upgraded to 13.5.1)
* Big Sur
* Monterey should work, but when booting the installer, it asked me to connect a mouse or a keyboard. Couldn't get to the installer UI.

Other close laptops (Huawei AMD from the same era) may work too, but it's unsure.

## What works and what doesn't

### Working

* GPU (see below for details)
* USB (kind of, see below for details)
* Keyboard
* Type-C video out
* HDMI (with audio)
* Brightness (keys work too)
* Sleep (seems to work, not sure if the system goes really into sleep and reduce power usage but you can wake it up after)
* Battery

### What doesn't work

* Trackpad (connected via I2C, maybe a kext for AMD exists, but not my priority)
* Internal audio (speakers & jack: Not tried because I use USB audio anyway, may look into the future)
* Wi-Fi: Will never work with the installed chip, but the M.2 slot is user serviceable, so you can put a supported module here.
* Fingerprint Reader: No drivers for Linux, so don't expect to get that supported.

## GPU

GPU acceleration is obtained by using [NootedRed](https://github.com/ChefKissInc/NootedRed). This patches macOS drivers to support the IGPU found in the mobile chip. While extremely exciting, it's very new and thus features are missing. GPU acceleration is a hit or miss. Furthermore, there is no access to the video encoders.

### Tested apps

* Chrome (works, but makes the system very laggy for 20 seconds, unless you disable material acceleration)
* Electron-based apps seem to suffer the same way Chrome does. Disabling GPU acceleration might help, if the option is available.
* Adobe apps: Hit or miss, even with AMDFriend.
    * Premiere: Seems again related to GPU acceleration. It seems to me that the program tries to initialize it, but it fails and then crashes.
    * After Effects: Same.
    * Media Encoder: Works, but using only the CPU.
    * Other apps: Not tested.

## USB

This laptop uses two USB controllers. This makes macOS unhappy and if both are activated, it will not boot.

Therefore, if you want to make a RyzenTosh, you need to disable one:

You can do this using this tool: [UMAF](https://github.com/DavidS95/Smokeless_UMAF):

Follow the instructions there and go to:

* Device manager -> AMD CBS -> FCH -> USB

* Disable one of these controllers:
    * XHCI0 is used by the Type-C and LEFT USB-A port.
    * XHCI1 is used by the two RIGHT USB ports and the Fingerprint reader.

**Do not disable both ports at the same time.** You will lose all USB ports, and you will not be able to boot the tool again to correct your mistake.

# Now, let's install

## Create your own SSDTs

While it may work just by using those provided here, it's better to use your actual hardware to create those.

Follow the instructions from the [NootedRed docs](https://nootinc.github.io/guide/gathering-files/acpi/) (Site seems down at the time of writing).


## Creating the macOS installer

I did mine using the offline method.

The offline method requires you to use macOS (see [Mist](https://github.com/ninxsoft/Mist)), whereas the online method can be used in Linux & Windows, but you will need an internet connection during setup.

You will then need a USB to Ethernet adapter. I might update and include Horndis in the kexts, but I'm not 100% sure it will work in the recovery.

Go [here](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/) for more details.

## EFI

Copy the EFI folder from this repo to the EFI partition on your USB drive. Replace SSDT *.aml files if you built your own, and apply the patches_oc.plist as stated in the tutorial.

## Booting the installer

Now that you made the installer, it's time to boot. You should soon see the macOS installer UI.

## Be patient.

During install, the computer will reboot multiple times. It may stall at some point with something close to:

`IOKit Daemon (kernelmanagerd) stall[0], (240s): 'GPU'`

Please be patient. In Big Sur, on my computer, the message appeared 4 times, and then the install continued. This will also appeer in system updates, but not during a normal boot.

## Serial Number

The config.plist found here doesn't (if it does, it's a mistake) contain a serial number.

Follow the steps [here](https://dortania.github.io/OpenCore-Post-Install/universal/iservices.html#using-gensmbios) to make your own.

## Credits

Coming soon

