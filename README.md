# Lenovo-UEFI-Unlocker

A backup of [SmokelessRuntimeEFIPatcher](https://github.com/SmokelessCPUv2/SmokelessRuntimeEFIPatcher).

## How to use it

- Download the zip file from the Release Page
- Extract files in a bootable USB drive
- Turn off safe boot in UEFI settings
- Boot it.

## Install it on the EFI System Partition (no USB drive needed)

Instead of using a USB drive, you can install it on the EFI System Partition (ESP) of the internal drive. Commands below are for Linux and assume the ESP is mounted on `/boot/efi` (check with `findmnt /boot/efi` or `lsblk -o NAME,FSTYPE,MOUNTPOINT`).

1. Turn off Secure Boot in the UEFI settings.
2. Copy the files to the ESP. The loader goes in its own folder, so nothing that is already on the ESP (like your OS bootloader or `EFI/BOOT/BOOTX64.efi`) gets overwritten. `SREP_Config.cfg` goes in the root of the ESP:

   ```sh
   sudo mkdir -p /boot/efi/EFI/SREP
   sudo cp EFI/BOOT/BOOTX64.efi /boot/efi/EFI/SREP/BOOTX64.efi
   sudo cp SREP_Config.cfg /boot/efi/SREP_Config.cfg
   ```

3. Add a boot entry for it. Replace `/dev/nvme0n1` and `--part 1` with the disk and partition number of your ESP:

   ```sh
   sudo efibootmgr --create-only --disk /dev/nvme0n1 --part 1 \
     --label "SREP Unlocker" --loader '\EFI\SREP\BOOTX64.efi'
   ```

4. Reboot, open the boot menu (usually `F12` on Lenovo devices) and choose **SREP Unlocker**. `--create-only` leaves your normal boot order unchanged, so the unlocked UEFI settings are only loaded when you pick this entry.

To remove it again, delete the entry with `sudo efibootmgr --bootnum XXXX --delete-bootnum` (`XXXX` is the number shown by `sudo efibootmgr`), then remove `/boot/efi/EFI/SREP` and `/boot/efi/SREP_Config.cfg`.

> If your firmware ignores entries added with `efibootmgr`, you can copy `BOOTX64.efi` to `EFI/BOOT/BOOTX64.efi` on the ESP instead. That is the fallback path firmware boots by default, so back up any file that is already there first.

## for Legion Intel 2022 user you need to replace the `SREP_Config.cfg` with this

```text
Op Loaded
H2OFormBrowserDxe
Op Patch
Pattern
49D592C3EB27464F8A119F5DF55A9C8B00000000
49D592C3EB27464F8A119F5DF55A9C8B01000000
Op Patch
Pattern
1AB0E0C17E60754BB8BB0631ECFAACF200000000
1AB0E0C17E60754BB8BB0631ECFAACF201000000
Op Patch
Pattern
9E76D4C6487F2A4D98E987ADCCF35CCC00000000
9E76D4C6487F2A4D98E987ADCCF35CCC01000000
Op Patch
Pattern
732871A65F92C64690B4A40F86A0917B00000000
732871A65F92C64690B4A40F86A0917B01000000
Op End

Op LoadFromFV
SetupUtilityApp
Op Exec
```
