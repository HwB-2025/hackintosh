# Monterey Install on GA-B75M-D3H

| Component | Detail |
| --- | --- |
| Motherboard | [Gigabyte GA-B75M-D3Hv1.1](https://www.gigabyte.com/Motherboard/GA-B75M-D3H-rev-11/sp#sp) |
| CPU | [Intel Xeon E3-1240 v2](https://ark.intel.com/content/www/us/en/ark/products/65730/intel-xeon-processor-e3-1240-v2-8m-cache-3-40-ghz.html) |
| GPU | Sapphire Pulse Radeon RX 580 8GB GDDR5 |
| Disk | Samsung SSD 850 EVO 250GB & SanDisk SDSSDXPS240G |
| OpenCore | 0.7.4 |
| Kexts | AppleALC 1.6.5 |
| | Lilu 1.5.6 |
| | RealtekRTL8111 2.4.2 |
| | VirtualSMC 1.2.7 |
| | WhateverGreen 1.5.4 |

## Key aspects

- Notes about upgrading from 0.7.1 to 0.7.4 OC:
 - 0.7.1 -> 0.7.2: https://www.reddit.com/r/hackintosh/comments/owofna/how_to_opencore_071_072_differences/
 - 0.7.2 -> 0.7.3: https://www.reddit.com/r/hackintosh/comments/pjjv7a/how_to_opencore_072_073_differences/
 - 0.7.3 -> 0.7.4: https://www.reddit.com/r/hackintosh/comments/q27ev6/how_to_opencore_073_074_differences/
 - Followed the changes listed for config.plist in each link.  Also
   had to make sure that `Base`, `BaseSkip`, `Mask`, `OemTableId`, and
   `ReplaceMask` were present in all the `Patch` entries under `ACPI`.
 - After updates, `ocvalidate` successfully reported no issues.

- `USBMap.kext` from the Catalina build was re-used again for
  Monterey.  That kext was produced via the standard USB mapping
  process.

- Kept all ACPI patches from Catalina.

- For `PlatformInfo`, the same serial numbers and product ID that were
  used for the Catalina and Big Sur builds.  No issues with Messages,
  etc were encountered.  DRM still works and Apple TV+ still works for
  DRM'd content.

- Building the USB: Using the standard method for building the USB on
  MacOS didn't work.  The installation failed part way through
  complaining that the USB was corrupted/damaged.  Rebuilding the USB
  on Linux using Method 2 worked great.  General steps for creating the USB:
```
# Partition command
sudo sgdisk -a -o -n 1:0:+200M -t 1:0700 -n 2:0:0 -t 2:af00 /dev/sdb
sudo mkfs.vfat -F 32 -n "MONTEREY_OC" /dev/sdb1

# Use OC/Utilities/macrecovery to download MacOS
cd OpenCore-0.7.4-RELEASE/Utilities/macrecovery
python ./macrecovery -b Mac-E43C1C25D4880AD6 -m 00000000000000000 download

# Find the "disk image" partition in BaseSystem.dmg and write that to USB.  This will take A LONG TIME.
sudo dmg2img -p $(dmg2img -l BaseSystem.dmg | grep "disk image" | awk '{ gsub(":", ""); print $2 }') -i BaseSystem.dmg -o /dev/sdb2

# Mount the EFI partition
sudo mkdir -p /mnt/efi && sudo mount /dev/sdb1 /mnt/efi

# Create necessary directories
sudo mkdir /mnt/efi/com.apple.recovery.boot
sudo mkdir /mnt/efi/EFI

# From here, copy the EFI files to /mnt/efi/EFI/
```
