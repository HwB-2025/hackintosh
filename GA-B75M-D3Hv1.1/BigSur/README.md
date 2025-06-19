# BigSur Install on GA-B75M-D3H

| Component | Detail |
| --- | --- |
| Motherboard | [Gigabyte GA-B75M-D3Hv1.1](https://www.gigabyte.com/Motherboard/GA-B75M-D3H-rev-11/sp#sp) |
| CPU | [Intel Xeon E3-1240 v2](https://ark.intel.com/content/www/us/en/ark/products/65730/intel-xeon-processor-e3-1240-v2-8m-cache-3-40-ghz.html) |
| GPU | Sapphire Pulse Radeon RX 580 8GB GDDR5 |
| Disk | Samsung SSD 850 EVO 250GB & SanDisk SDSSDXPS240G |
| OpenCore | 0.7.1 |
| Kexts | AppleALC 1.6.2 |
| | Lilu 1.5.4 |
| | RealtekRTL8111 2.4.2 |
| | VirtualSMC 1.2.5 |
| | WhateverGreen 1.5.1 |

## Key aspects

- Starting point was the {Sandy|Ivy} Bridge-E config guide on the Dortania site:  https://dortania.github.io/OpenCore-Install-Guide/config-HEDT/ivy-bridge-e.html

- `SecureBootModel` needed to be set to `Disabled` in `config.plist` under `Misc/Security`.  With this set to the default value of 'Default', the system would hang on boot.

- Building the USB stick on a Mac yielded an unbootable stick.  There was some files missing from where the installer expected after choosing a boot option in the OpenCore menu.  Rebuilding the USB stick using the Linux creation instructions worked.

- Initial boot for install was done with `SSDT-EC.aml` and `SSDT-UNC.aml` only.  `SSDT-PM.aml`, `SSDT-PLUG.aml`, and `SSDT-GPRW.aml` were added in after initial install.  `SSDT-PM.aml` was generated via ssdtPRgen.  `SSDT-PLUG.aml` was produced via SSDTTime.  `SSDT-GRPW.aml` was the pre-built one available.

- `USBMap.kext` from the Catalina build was re-used for Big Sur.  That kext was produced via the standard USB mapping process.

- The following ACPI patches were used for the production `config.plist`:
```xml
<dict>
				<key>Comment</key>
				<string>XHC1 to SHCI</string>
				<key>Count</key>
				<integer>0</integer>
				<key>Enabled</key>
				<false/>
				<key>Find</key>
				<data>WEhDMQ==</data>
				<key>Limit</key>
				<integer>0</integer>
				<key>Replace</key>
				<data>U0hDSQ==</data>
				<key>Skip</key>
				<integer>0</integer>
				<key>TableLength</key>
				<integer>0</integer>
				<key>TableSignature</key>
				<data></data>
			</dict>
			<dict>
				<key>Comment</key>
				<string>EHC1 to EH01</string>
				<key>Count</key>
				<integer>0</integer>
				<key>Enabled</key>
				<true/>
				<key>Find</key>
				<data>RUhDMQ==</data>
				<key>Limit</key>
				<integer>0</integer>
				<key>Replace</key>
				<data>RUgwMQ==</data>
				<key>Skip</key>
				<integer>0</integer>
				<key>TableLength</key>
				<integer>0</integer>
				<key>TableSignature</key>
				<data></data>
			</dict>
			<dict>
				<key>Comment</key>
				<string>change Method(GPRW,2,N) to XPRW, pair with SSDT-GPRW.aml</string>
				<key>Count</key>
				<integer>0</integer>
				<key>Enabled</key>
				<true/>
				<key>Find</key>
				<data>R1BSVwI=</data>
				<key>Limit</key>
				<integer>0</integer>
				<key>Mask</key>
				<data></data>
				<key>OemTableId</key>
				<data></data>
				<key>Replace</key>
				<data>WFBSVwI=</data>
				<key>ReplaceMask</key>
				<data></data>
				<key>Skip</key>
				<integer>0</integer>
				<key>TableLength</key>
				<integer>0</integer>
				<key>TableSignature</key>
				<data></data>
			</dict>
```

- To get DRM working, `shikigva` and `unfairgva` were injected to the GPU via `DeviceProperties`.  It's not clear if `shikigva` was needed as it alone didn't get DRM working.
```xml
	<key>DeviceProperties</key>
	<dict>
		<key>Add</key>
		<dict>
			<key>PciRoot(0x0)/Pci(0x1,0x0)/Pci(0x0,0x0)</key>
			<dict>
				<key>shikigva</key>
				<integer>128</integer>
				<key>unfairgva</key>
				<data>AQAAAA==</data>
			</dict>
		</dict>
		<key>Delete</key>
		<dict/>
	</dict>
```

- For `PlatformInfo`, the same serial numbers and product ID that were used for the Catalina build were re-used on the Big Sur build.  No issues with Messages, etc were encountered.
