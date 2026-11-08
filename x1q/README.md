# x1q — Galaxy S20 5G (SC-51A / SM-G981N / SM-G9810 / SCG01)

Device binaries for the `x1q` UEFI port (SM8250 kona).

## Firmware used

```
BL_SC51AOMS1DWL1_SC51AOMS1DWL1_MQB75511663_REV00_user_low_ship_MULTI_CERT
```

Region DCM (Docomo), build `...DWL1`.

## Verified from the device

`RawFiles/uefiplat.cfg` was carved out of `xbl.elf` in the BL package above
(`[Config]` block, 177 lines). It is the authoritative memory map and UEFI
platform configuration for this device.

Compared against the r8q copy:

- all 146 non-empty lines identical
- all 38 `[ConfigParameters]` entries identical
- UEFI FD base `0x9FC00000`, size `0x300000`
- DXE heap `0x98900000`, display reserved `0x9C000000`, UEFI stack `0x9FF90000`

## Bootstrapped from r8q (not verified on hardware)

Everything else is copied from `r8q` (Galaxy S20 FE Snapdragon, same SM8250 SoC):

- `QcomPkg/` driver binaries, including the r8q patches:
  - `ButtonsDxe` — Samsung key code `0x80` remapped to Enter, unicode key handling
  - `DALSYSDxe` — cache coherence enabled for UFS
  - `TzDxeLA` — global TZ applet variable set to `TRUE`
  - `UsbConfigDxe` — IOMMU detach removed from ExitBootServices
  - `UsbMsdDxe` — removable state set to non-removable
- `RawFiles/uefipil.cfg`, `RawFiles/SecParti.cfg` — the stock SC-51A `abl.elf`
  does not carry these as raw sections (checked in the decompressed ABL FV), so
  the r8q versions are used as generic kona defaults.
- Panel XMLs — 10 panel definitions were found in SC-51A `xbl.elf`
  (`AMS622NP01`, `AMS623MR01`, `AMB622NP01`, ...), but the build does not
  reference panel XMLs at all (`USE_CUSTOM_DISPLAY_DRIVER = 0`, SimpleFb), so the
  r8q generic set is kept.

## Status

Buildable bootstrap. Not yet validated on an SC-51A: display, UFS, buttons and
USB still need hardware testing.
