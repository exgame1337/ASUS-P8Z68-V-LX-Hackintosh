# Asus-P8Z68-V-LX-Hackintosh

Hackintosh for [Asus P8Z68-V LX](https://www.asus.com/Motherboards/P8Z68V_LX/) motherboard using OS X 10.10 Yosemite.  
This is a minimal guide that fits my hardware configuration.

Intel Z68 chipset, LGA1155 socket.  
Supports 2nd gen. ([32 nm - Sandy Bridge](http://en.wikipedia.org/wiki/Sandy_Bridge)) Intel Core CPUs.

Onboard devices:
- Realtek 8111E Gigabit LAN controller
- Realtek ALC887 8-Channel High Definition Audio CODEC
- ASMedia USB 3.0 controller (the 2 blue USB ports at the back panel)

## BIOS Settings

Latest stable BIOS: version [4105 (2013/07/23 update, 2013/07/01 build date)](https://www.asus.com/Motherboards/P8Z68V_LX/HelpDesk_Download/)
- F5: Optimized Defaults
- Boot > Setup Mode > Advanced Mode

You can activate [Intel Virtualization Technology (VT-x)](http://en.wikipedia.org/wiki/X86_virtualization#Intel_virtualization_.28VT-x.29) once OS X is installed:
- Advanced > CPU Configuration > Intel Virtualization Technology - Enabled

## Patched BIOS to avoid kernel panic with AppleIntelCPUPowerManagement.kext

You can use NullCPUPowerManagement.kext (no Intel SpeedStep and no sleep) or patch the BIOS using [UEFIPatch](https://github.com/LongSoft/UEFITool):
```Shell
curl -O http://dlcdnet.asus.com/pub/ASUS/mb/LGA1155/P8Z68-V_LX/P8Z68-V-LX-ASUS-4105.zip
open P8Z68-V-LX-ASUS-4105.zip
curl -OL https://github.com/LongSoft/UEFITool/releases/download/0.20.5/UEFIPatch_0.3.5_osx.zip
open UEFIPatch_0.3.5_osx.zip
cd UEFIPatch_0.3.5_osx
./UEFIPatch ../P8Z68-V-LX-ASUS-4105.ROM # Generates file `P8Z68-V-LX-ASUS-4105.ROM.patched`
```

/!\ This operation can brick your motherboard, do it at your own risk /!\  
Flash your BIOS using 'ASUS EZ Flash 2 Utility' and file `P8Z68-V-LX-ASUS-4105.ROM.patched`.

Sources:
- [Power Management on Asus 1155 Motherboards](http://www.tonymacx86.com/bios-uefi/43486-asus-1155-patched-bios-repository.html)
- [[UEFIPatch] UEFI patching utility](http://www.insanelymac.com/forum/topic/285444-uefipatch-uefi-patching-utility/)

## DSDT

My tests (sleep, wake, shutdown...) have concluded that there is no need to generate a patched `DSDT.aml` file.

## MultiBeast

Using version 7.x

Beside defaults, check/uncheck:
- Quick Start > DSDT Free
- Drivers > Audio > Realtek ALCxxx > ALC887/888b Current
- ~~Drivers > Disk > 3rd Party SATA~~
- Drivers > Disk > TRIM Enabler (if you own a SSD disk)
- Drivers > Misc > USB 3.0 - Universal
- Drivers > Network > Realtek > RealtekRTL8111
- Customize > Boot Options > Verbose Boot (if you want to see what's going on at boot time)
- ~~Customize > Boot Options > Generate CPU States~~
- Customize > System Definitions > iMac > iMac 12,2
- Customize > SSDT Options > Sandy Bridge Core i7 (or Core i5 depending on your CPU)

Sources:
- [Native Ivy Bridge CPU and GPU Power Management](http://www.tonymacx86.com/mountain-lion-desktop-support/86807-ml-native-ivy-bridge-cpu-gpu-power-management.html)

## Performance

Using [Geekbench](http://www.primatelabs.com/geekbench/), you should get a score (Intel Core i7-2700K @ 3.50 GHz) > 3000 (single-core) > 11000 (multi-core).

## License

Do whatever you like, this is public domain.