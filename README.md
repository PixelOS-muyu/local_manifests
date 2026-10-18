# PixelOS 16 for the Xiaomi Pad 7 Pro (muyu) – UNOFFICIAL

Unofficial build of [PixelOS](https://github.com/PixelOS-AOSP) (`sixteen-qpr2`, Android 16 QPR2)
for the **Xiaomi Pad 7 Pro** (`muyu`, Snapdragon 8s Gen 3 / SM8635).
This is not an official PixelOS build and is not supported by the PixelOS team.

**Download:** https://sourceforge.net/projects/pixelos-muyu/files/

## Features

- Pixel experience with Google apps included
- **Xiaomi Focus Keyboard works out of the box** – no root, no modules
  (touchpad, Fn keys, Caps Lock LED, adaptive backlight just like on HyperOS)
- **Performance tile** in Quick Settings: Balanced / Performance / Maximum,
  using Xiaomi's own thermal profiles – great for emulators
- Fingerprint, desktop mode when the keyboard is attached

## What works

Display (144 Hz), touch, audio, camera, Wi-Fi, Bluetooth, fingerprint, keyboard. No known battery drain.

## Notes

- No unlimited Google Photos backup (the Pixel spoof caused an update loop in Photos).

## Requirements

- Unlocked bootloader
- **HyperOS 3 (Android 16) Global firmware**, tested on `OS3.0.303.0.WOYMIXM`
  (the ROM does not flash firmware)

## Installation (clean install)

Files from the release: `PixelOS_muyu-*.zip`, `boot.img`, `init_boot.img`, `vendor_boot.img`,
`dtbo.img`, `recovery.img`, plus LineageOS' `copy-partitions-20220613-signed.zip`.

```
adb reboot bootloader          # or: adb reboot fastboot (fastbootd), see note below
fastboot flash dtbo dtbo.img
fastboot flash vendor_boot vendor_boot.img
fastboot flash boot boot.img
fastboot flash init_boot init_boot.img
fastboot flash recovery recovery.img
fastboot reboot recovery
```

In the PixelOS recovery:

1. *Apply update → Apply from ADB*, `adb sideload copy-partitions-20220613-signed.zip`
2. *Factory reset → Format data / factory reset*
3. *Advanced → Reboot to recovery* (otherwise: "Logical partitions are mapped")
4. *Apply update → Apply from ADB*, `adb sideload PixelOS_muyu-*.zip`
   (stopping at ~47 % is normal; if asked "Signature verification failed", answer *Yes*)
5. Answer *No* to "install additional packages", then *Reboot system now*

Updates: `adb reboot sideload` and `adb sideload PixelOS_muyu-*.zip`, no wipe needed.

**Windows + AMD Ryzen:** the bootloader fastboot is often not detected on Ryzen USB controllers.
Use fastbootd instead (`adb reboot fastboot`); if Windows has no driver for it, assign the
generic *WinUsb* driver, or Google's USB driver for the PixelOS fastbootd (PID 4EE0).

## Building

```
repo init -u https://github.com/PixelOS-AOSP/android_manifest.git -b sixteen-qpr2 --git-lfs
mkdir -p .repo/local_manifests
curl -o .repo/local_manifests/muyu.xml https://raw.githubusercontent.com/PixelOS-muyu/local_manifests/sixteen-qpr2/muyu.xml
repo sync -c -j8 --no-tags --no-clone-bundle
. build/envsetup.sh && breakfast muyu user && m pixelos
```

Release builds are signed with private keys (`vendor/lineage-priv/keys`), which are not published.
Without them the build is signed with the AOSP test keys.
With your own keys, `keys.mk` needs both lines below (otherwise Bluetooth crashes):

```
PRODUCT_DEFAULT_DEV_CERTIFICATE := vendor/lineage-priv/keys/releasekey
PRODUCT_MAINLINE_BLUETOOTH_SEPOLICY_DEV_CERTIFICATES := vendor/lineage-priv/keys
```

The keys directory also needs `testkey` (referenced by `system/sepolicy/private/keys.conf`).

## Sources

| Repository | Based on |
|---|---|
| [device_xiaomi_muyu](https://github.com/PixelOS-muyu/android_device_xiaomi_muyu) | Xiaomi-Pad-7-Pro-Resources, `lineage-23.2` |
| [device_xiaomi_sm8635-common](https://github.com/PixelOS-muyu/android_device_xiaomi_sm8635-common) | Xiaomi-Pad-7-Pro-Resources, `lineage-23.2` (Feb 2026) |
| [vendor_xiaomi_muyu](https://github.com/PixelOS-muyu/android_vendor_xiaomi_muyu) | Xiaomi-Pad-7-Pro-Resources, OS3.0.7.0 + MiDevAuth from OS3.0.303.0 |
| [vendor_xiaomi_sm8635-common](https://github.com/PixelOS-muyu/android_vendor_xiaomi_sm8635-common) | Xiaomi-Pad-7-Pro-Resources |
| [device_xiaomi_muyu-kernel](https://github.com/Xiaomi-Pad-7-Pro-Resources/android_device_xiaomi_muyu-kernel) | used as is (prebuilt stock kernel) |
| [device_lineage_sepolicy](https://github.com/PixelOS-muyu/android_device_lineage_sepolicy) | LineageOS, `lineage-23.2` |
| [frameworks_base](https://github.com/PixelOS-muyu/android_frameworks_base) | PixelOS, `sixteen-qpr2` (Google Photos spoof opt-in) |

Kernel source: [Xiaomi-Pad-7-Pro-Resources/android_kernel_xiaomi_sm8635](https://github.com/Xiaomi-Pad-7-Pro-Resources/android_kernel_xiaomi_sm8635).

## Credits

- [PixelOS](https://github.com/PixelOS-AOSP) and [LineageOS](https://github.com/LineageOS)
- **dodyirawan85 / IrawansProjekt** and Xiaomi-Pad-7-Pro-Resources for the muyu / sm8635 device,
  vendor and kernel trees
- **JohnnyFFM** for [xiaomi-keyboard-fix](https://github.com/JohnnyFFM/xiaomi-keyboard-fix)
  (MIT), whose keyboard protocol work the native keyboard daemon is based on
- PixelOS-Devices (peridot) for the PixelOS SM8635 reference tree
