
 **IMPORTANT INFORMATION READ THIS FIRST**:
 - Opencore Legacy Patcher does *not* officially support being run on non-Apple Hardware. The OCLP Discord does ***not*** provide any support for hacks using OCLP.
 - *The Hackintosh paradise discord provides limited support for Hack's running root patching of any kind, including OCLP. If you are not comfortable with patching your system, or maintaining a root patched system you should remain on a version of macOS that supports your hardware.*
 
Original instructions provided by OCLP team can be referenced [here](https://github.com/dortania/OpenCore-Legacy-Patcher/pull/1077#issuecomment-1646934494). 

OCLP can be downloaded [here](https://github.com/dortania/OpenCore-Legacy-Patcher).
## Pre-Root Patching

 - SIP (System Integrity Protection) must be set to a reduced state, 0x803. In your `config.plist` make sure the following value is set under `NVRAM>Add>7C436110-AB2A-4BBB-A880-FE41995C9F82`.
	 - ``csr-active-config | data | 03080000``
		 
		 **Note**: Add `csr-active-config` to `NVRAM>Delete>7C436110-AB2A-4BBB-A880-FE41995C9F82` as well or make sure to reset your NVRAM. Adding it to this section of your ``config.plist`` means OpenCore will take care of changing the value for you on reboot automatically.
 - AMFI set to disabled. In your `config.plist` add the following to your boot arguments under `NVRAM>Add7C436110-AB2A-4BBB-A880-FE41995C9F82`.
	 - ``boot-args | string | amfi=0x80``
- Secure Boot Model set to `Disabled`. Changing the secure boot status **requires** an NVRAM reset, if not some variables are retained which can cause issue with IMG4 verification in macOS. (Thanks to [Kronokernel](https://github.com/khronokernel) for the heads up!)
	
	- [ApECID](https://dortania.github.io/OpenCore-Post-Install/universal/security/applesecureboot.html#apecid) *cannot* be used with root patches, it needs to be disabled and remain disabled.
- **Block** kext: `com.apple.iokit.IOSkywalkFamily` (reference plist: availabe [here](https://github.com/dortania/OpenCore-Legacy-Patcher/blob/e21efa975c0cf228cb36e81a974bc6b4c27c7807/payloads/Config/config.plist#L1695-L1710), thanks OCLP team!) Make sure to set the `min kernel` value in your plist to 23.0.0 so this is only blocked for Sonoma if you are booting multiple OS versions.
![Block Example](https://i.imgur.com/48bNWgo.png)
- **Inject** kexts: [IOSkywalk.kext](https://github.com/dortania/OpenCore-Legacy-Patcher/blob/e21efa975c0cf228cb36e81a974bc6b4c27c7807/payloads/Kexts/Wifi/IOSkywalkFamily-v1.0.0.zip), [IO80211FamilyLegacy.kext](https://github.com/dortania/OpenCore-Legacy-Patcher/blob/e21efa975c0cf228cb36e81a974bc6b4c27c7807/payloads/Kexts/Wifi/IO80211FamilyLegacy-v1.0.0.zip) - **this one also has a child kext** `AirPortBrcmNIC.kext` make sure that is injected as well. For all three set the `min kernel` version to 23.0.0 so they are only injected in Somona if you are booting multiple OS versions.
![kernel add](https://i.imgur.com/jq7Lqcf.png)
- Once the changes have been applied, reboot, reset your NVRAM and then OpenCore Legacy Patcher should now show the option to apply root patches.


## Root Patching Process
- Once rebooted, open OpenCore Legacy Patcher, and click on `Post-Install Root Patch`.
	![OCLP Main Page](https://i.imgur.com/vO9kcGd.png)
	- A screen should pop up, showing your system as eligible for `Modern Wireless` patches. (*Other patch sets are unsupported on Hacks, apply at your own risk!*) 
	![Start Root Patching](https://i.imgur.com/NWdetZG.png)
	- Click `Start Root Patching` to have OCLP being to make its changes.
	
	 **NOTE:** OCLP needs root privleges in order to modify system files. If the application asks to re-start as root, make sure to click yes and provide your password. Alternatively you can run the application through Terminal with the `sudo` option to escalate the privleges!
	 
	 
	 ![Root Patching](https://i.imgur.com/IZjb79r.png)
	- When OCLP has finished patching, you'll get a pop up asking you to reboot. Reboot and you should have working wifi on your system! WOOHOO!
   
	![Reboots](https://i.imgur.com/WgK0H77.png)
	
# Additional Requirements:
- If the SMBIOS used on your hack uses a is a model with a [T2](https://dortania.github.io/OpenCore-Post-Install/universal/security/applesecureboot.html#securebootmodel) chip, you will not be able to apply updates through system settings without additional steps. In order to re-enable them, you need to use  [RestrictEvents.kext](https://github.com/acidanthera/RestrictEvents) with the boot-arg `revpatch=sbvmm`. This should allow you to then update through system settings normally.

# Other Important Notes: 
- Once your root volume has been patched, SIP must remain at least partially disabled (the settings applied to your config.plist), or ***you will not be able to properly boot your system***.
- Delta updates are unavailable to root patched sytems, so updates will show as the full 12GB+ installers. Though not recommended, it is possible to revert your patches and update, then re-apply them if you absolutely cannot download the full update, but do so at your own risk.
- AMFI can be partially re-enabled using [AMFIPass.kext](https://github.com/dortania/OpenCore-Legacy-Patcher/tree/main/payloads/Kexts/Acidanthera) from OCLP, and using the boot args `-lilubetaall` and `-amfipassbeta` in your NVRAM section. This can be handy if you're running into issues with an application you use related to AMFI.
- If you run into issues with Electron based apps after disabling sip, ie: *Discord*, *Google Chrome*, *VS Code*, you can try adding the following boot arg `ipc_control_port_options=0` NVRAM settings.



# *NO SUPPORT IS PROVIDED FOR THIS CONFIGURATION. IF YOU HAVE QUESTIONS READ THE DOCUMENTATION AND FIGURE THEM OUT.*

