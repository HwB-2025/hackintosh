# Monterey Install on GA-B75M-D3H

| Component | Detail |
| --- | --- |
| Motherboard | [Gigabyte GA-B75M-D3Hv1.1](https://www.gigabyte.com/Motherboard/GA-B75M-D3H-rev-11/sp#sp) |
| CPU | [Intel Xeon E3-1240 v2](https://ark.intel.com/content/www/us/en/ark/products/65730/intel-xeon-processor-e3-1240-v2-8m-cache-3-40-ghz.html) |
| GPU | Sapphire Pulse Radeon RX 580 8GB GDDR5 |
| Disk | Samsung SSD 850 EVO 250GB & SanDisk SDSSDXPS240G |
| OpenCore | 0.8.0 |
| Kexts | AppleALC 1.7.1 |
| | Lilu 1.6.0 |
| | RealtekRTL8111 2.4.2 |
| | VirtualSMC 1.2.9 |
| | WhateverGreen 1.5.8 |

## Key aspects

- Notes about upgrading from 0.7.4 to 0.8.0 OC:
  - 0.7.4 -> 0.7.5: https://www.reddit.com/r/hackintosh/comments/qkznkq/how_to_opencore_074_075_differences/
  - 0.7.5 -> 0.7.6: https://www.reddit.com/r/hackintosh/comments/rajdr4/how_to_opencore_075_076_differences/
  - 0.7.6 -> 0.7.7: https://www.reddit.com/r/hackintosh/comments/s10npk/how_to_opencore_076_077_differences/
  - 0.7.7 -> 0.7.8: https://www.reddit.com/r/hackintosh/comments/smyjv2/how_to_opencore_077_078_differences/
  - 0.7.8 -> 0.7.9: https://www.reddit.com/r/hackintosh/comments/t8zi4s/how_to_opencore_078_079_differences/
  - 0.7.9 -> 0.8.0: https://www.reddit.com/r/hackintosh/comments/u6g1tk/how_to_opencore_079_080_differences/
  - Changes:
    - Added Booter/Quirks/ResizeAppleGpuBar (-1, 0) & UEFI/Quirks/ResizeGpuBars.  Both -1.
    - Added UEFI/Output/UIScale and UEFI/Output/ReconnectGraphicsOnConnect
    - Added SystemAudioVolumeDB to NVRAM/Add/7C436110-AB2A-4BBB-A880-FE41995C9F82, UEFI/AppleInput/{PointerPollMin, PointerPollMax, PointerPollMask}, added several items to UEFI/Audio.
    - Added Kernel/Block[0]/Strategy and Misc/Debug/LogModules
    - Added Kernel/Quirks/{ForceAquantiaEthernet, CustomPciSerialDevice} and Misc/Serial/{Init, Override}.  Removed Misc/Debug/SerialInit.

- `USBMap.kext` from the Monterey build was re-used again for
  Monterey-12.3.  That kext was produced via the standard USB mapping
  process.

- Kept all ACPI patches from Monterey.

- For `PlatformInfo`, the same serial numbers and product ID that were
  used for the Catalina, Big Sur, Monterey builds.  No issues with Messages,
  etc were encountered.  DRM still works and Apple TV+ still works for
  DRM'd content.

- Building the USB: Failed at building the USB using the Linux method
  this time around.  Sources online hinted that this was due to
  `dmg2img` didn't properly do it's thing right.  I decided to use the
  Dortania Windows instructions to build the USB and then copied over
  the EFI files for OC 0.8.0.

- This upgrade involved migrating the existing system to a new 1TB
  SSD.  SuperDuper! was used for cloning and the only issues after
  that were applications needing to be re-validated.

- There was a BIOS corruption issue at one point when testing.
  Restored optimized BIOS defaults and just made sure that:
  - On-board GPU was disabled
  - ACPI was set for drive access
  - VT-D was enabled (VMs won't start without it, duh)

- The steps for upgrading were:
  - Detach primary drive and make sure that new EFI on a USB will boot
    into the installer.
  - Once that's confirmed, copy new EFI over to EFI partition on the
    existing drive.
  - Boot up and run upgrade from the System Preferences control panel.
  - Note: It's much easier to do this if there's a clone of the drive
    available to restore if something goes wrong.
