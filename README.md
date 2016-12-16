#EFI-boot-toggle

Still in early stages.

EFI program allowing for A/B split booting based on a linux-accessible EFI variable.

Standard use case is OTA-update implementations, where you have this program invoked by startup.nsh, and then have a boot_a.nsh and boot_b.nsh for each image.

EFI variable "BOOTTOGGLE" can be made accessible in Linux through `efivarfs` interface, which you can then set to "A" or "B" to boot from an updated image in-place, while allowing fallback for failed updates.  An init script is used to set update flag and boot count to zero on successful boot.

#### efi actions:
- if `UPDATEFLAG` == 0:
    - boot `BOOTTOGGLE`
- if `UPDATEFLAG` == 1:
    - if `BOOTCOUNT` == 0 (not yet attempted):
        - `BOOTCOUNT` = 1
        - boot `BOOTTOGGLE`
    - if `BOOTCOUNT` == 1 (boot from new image unsucessful):
        - `BOOTTOGGLE` = opposite script
        - `UPDATEFLAG` = 0
        - `BOOTCOUNT` = 0
        - boot modified `BOOTTOGGLE`

#### linux init script actions:
- if `UPDATEFLAG` == 0:
    - do nothing
- if `UPDATEFLAG` == 1 (boot must have been successful):
    - `UPDATEFLAG` = 0
    - `BOOTCOUNT` = 0

- `BOOTTOGGLE` is set during update
