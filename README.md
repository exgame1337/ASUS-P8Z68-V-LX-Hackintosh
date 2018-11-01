# Asus-P8Z68-V-LX-Hackintosh

UPDATED TO macOS 10.13 High Sierra

Hackintosh for [ASUS P8Z68-V LX](https://www.asus.com/Motherboards/P8Z68V_LX/) motherboard using macOS 10.13 High Sierra.
This is a minimal guide for personal use that fits my hardware configuration.

Intel Z68 chipset, LGA1155 socket.
Supports 2nd gen. ([32 nm - Sandy Bridge](http://en.wikipedia.org/wiki/Sandy_Bridge)) Intel Core CPUs.

Onboard devices:
- Realtek 8111E Gigabit LAN controller
- Realtek ALC887 8-Channel High Definition Audio CODEC
- ASMedia USB 3.0 controller (the 2 blue USB ports at the back panel)

## BIOS Settings

Latest stable BIOS: version [4105 (2013/07/23 update, 2013/07/01 build date)](https://www.asus.com/Motherboards/P8Z68V_LX/HelpDesk_Download/)
- Del: enter the BIOS setup
- F8: display the boot menu

- F5: reset to default settings (Optimized Defaults)
- Advanced > USB Configuration > EHCI Hand-off: Enabled (optional)
- Advanced > Onboard Devices Configuration > Serial Port Configuration > Serial Port: Disabled (optional)
- Boot > Full Screen Logo: Disabled (optional)
- Boot > Setup Mode: Advanced Mode (optional)

Do not touch option "Boot > PCI ROM Priority", in my case it crashes the BIOS and a clear CMOS is then needed

## Patched BIOS to avoid kernel panic with AppleIntelCPUPowerManagement.kext

You can use NullCPUPowerManagement.kext (no Intel SpeedStep and no sleep) or patch the BIOS using [UEFIPatch](https://github.com/LongSoft/UEFITool):
```Shell
curl -O http://dlcdnet.asus.com/pub/ASUS/mb/LGA1155/P8Z68-V_LX/P8Z68-V-LX-ASUS-4105.zip
open P8Z68-V-LX-ASUS-4105.zip
curl -OL https://github.com/LongSoft/UEFITool/releases/download/0.20.5/UEFIPatch_0.3.5_osx.zip
open UEFIPatch_0.3.5_osx.zip
cd UEFIPatch_0.3.5_osx
./UEFIPatch ../P8Z68-V-LX-ASUS-4105.ROM # Generates P8Z68-V-LX-ASUS-4105.ROM.patched
```

/!\ This operation can brick your motherboard, do it at your own risk /!\
Flash your BIOS using "ASUS EZ Flash 2 Utility" and file `P8Z68-V-LX-ASUS-4105.ROM.patched`.

Sources:
- [Power Management on Asus 1155 Motherboards](http://www.tonymacx86.com/bios-uefi/43486-asus-1155-patched-bios-repository.html)
- [[UEFIPatch] UEFI patching utility](http://www.insanelymac.com/forum/topic/285444-uefipatch-uefi-patching-utility/)

## DSDT

My tests (sleep, wake, shutdown...) have concluded that there is no need to generate a patched `DSDT.aml` file.

## MultiBeast

Using version 10.4.X

- Quick Start > UEFI Boot Mode
- Drivers > Audio > Realtek ALCxxx > ALC887/888b
- Drivers > Misc > FakeSMC
- Drivers > Network > Realtek > RealtekRTL8111
- Drivers > USB > 3rd Party USB 3.0
- Bootloaders > Clover UEFI Boot Mode
- Customize > System Definitions > iMac > iMac 14,2
- Customize > SSDT Options > Sandy Bridge Core i5 (or Core i7 depending on your CPU)

Sources:
- [ASUS P8Z68-V PRO/GEN3 Hackintosh - Multibeast 8.x](https://github.com/DavidGoldman/ASUS-P8Z68-V-PRO-GEN3-Hackintosh/blob/1b4146189ed79fee06d1c3515e524af2fb5e792e/README.md#multibeast-8x)

## Mount EFI partition

```Shell
diskutil list
sudo mkdir /Volumes/EFI
sudo mount_msdos /dev/disk0s1 /Volumes/EFI
```

## Verbose mode

Add `-v` flag to file `/Volumes/EFI/EFI/CLOVER/config.plist`:
```XML
<key>Arguments</key>
<string>dart=0 -v</string>
```

## Enable trim

If you own a SSD you should enable TRIM support:

```Shell
sudo trimforce enable
```

## Disable Gatekeeper

```Shell
sudo spctl --master-disable
```


## BIOS boot entry

The ASUS P8Z68 UEFI BIOS will recognize a USB key configured with Clover "Install for UEFI booting only" but not a hard drive.

Using old Clover versions you could boot on using Clover USB key and add the boot entry for your hard drive into the BIOS using "Clover Boot Options" > "Add Clover boot options for all entries".
This is not possible anymore (why?), instead you will need to manually add the boot entry.

Open "Clover > Start UEFI Shell 64" and play with `bcfg`:

```Shell
map fs* # Show all partitions
fs0: # Switch to fs0, fs1, fs2... partition
ls # List the partition content
ls EFI/BOOT
bcfg boot dump # List current boot entries
bcfg boot add N EFI/BOOT/BOOTX64.EFI "Clover" # Add EFI/BOOT/BOOTX64.EFI as a boot entry labeled "Clover", N being 0 (first boot entry), 1 (second boot entry)...
bcfg boot rm N # Remove a boot entry given its number N in the list
exit # Get back to Clover main screen
reset # Restart the computer
```

## License

Do whatever you like, this is public domain.
