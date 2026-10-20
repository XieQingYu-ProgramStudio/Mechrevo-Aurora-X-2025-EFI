# Third-Party Notices

This repository redistributes binaries produced by third-party projects. The
GNU General Public License v3.0 in [LICENSE](LICENSE) applies to the original
documentation, configuration arrangement, and sanitized configuration edits
made for this repository. It does not replace or override any upstream
license.

## Components

| Component | Version | Upstream |
| --- | ---: | --- |
| OpenCorePkg / OpenCore.efi / OpenCanopy.efi / OpenRuntime.efi / ResetNvramEntry.efi | DBG-109-2026-10-17 | https://github.com/acidanthera/OpenCorePkg |
| Lilu | 1.7.3 | https://github.com/acidanthera/Lilu |
| VirtualSMC and SMC plugins | 1.3.9 | https://github.com/acidanthera/VirtualSMC |
| WhateverGreen | 1.7.2 | https://github.com/acidanthera/WhateverGreen |
| NVMeFix | 1.1.4 | https://github.com/acidanthera/NVMeFix |
| RestrictEvents | 1.1.7 | https://github.com/acidanthera/RestrictEvents |
| BrightnessKeys | 1.0.4 | https://github.com/acidanthera/BrightnessKeys |
| VoodooPS2Controller | 2.3.8 | https://github.com/acidanthera/VoodooPS2 |
| CpuTopologyRebuild | 2.0.2 | https://github.com/b00t0x/CpuTopologyRebuild |
| AirportItlwm_Sequoia | 2.4.0 | https://github.com/OpenIntelWireless/itlwm |
| IntelBluetoothFirmware / IntelBTPatcher | 2.5.0 | https://github.com/OpenIntelWireless/IntelBluetoothFirmware |
| BlueToolFixup | 2.7.3 | Distributed with IntelBluetoothFirmware or BrcmPatchRAM releases |
| RTL812xLucy | 1.1.100 | https://github.com/Mieze/RTL812xLucy |
| VoodooI2C / VoodooI2CHID | 2.9.1 / 1 | https://github.com/VoodooI2C/VoodooI2C |
| USBToolBox / UTBDefault | 1.2.0 / 1.0 | https://github.com/USBToolBox/kext |
| AMFIPass | 1.4.1 | Upstream distribution referenced by the repository maintainer |
| VoodooHDA_BL | 3.7.0 | VoodooHDA-derived binary; retain the upstream project license |
| XHCI-unsupported | 0.9.2 | USB injector maintained by the Hackintosh community |
| HfsPlus.efi | Unversioned | Apple platform EFI driver; redistribution restrictions may apply |
| NullMothSafe.efi | Unversioned | NullMoth-related maintenance tool supplied with this EFI |

## License handling

Some binary distributions do not embed license files. Before redistributing or
modifying a component, verify the current upstream license and source release.
The included versions are recorded for traceability, not as a claim that every
binary is covered by one common license.

Projects with clear upstream licenses include:

- OpenCorePkg, Lilu, WhateverGreen, VirtualSMC, RestrictEvents,
  BrightnessKeys and CpuTopologyRebuild: BSD-3-Clause.
- AirportItlwm / itlwm: GPL-2.0.
- IntelBluetoothFirmware: GPL-3.0.
- VoodooI2C: GPL-3.0.

`HfsPlus.efi` is distributed for boot compatibility but is not an open-source
Acidanthera component. Remove or replace it if your distribution policy does
not permit redistribution.

## NVIDIA system-side components

The following NullMoth kernel extensions are **not included** in this EFI:

```text
NVRM.kext
NVRMAGDC.kext
NVRMFB.kext
NVAccel.kext
```

They are installed in the macOS system volume and are outside the scope of this
repository. Obtain them only from their upstream provider and follow that
provider's license and installation instructions.
