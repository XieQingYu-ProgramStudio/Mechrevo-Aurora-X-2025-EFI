# 机械革命 极光 X 2025 OpenCore EFI

适用于 **机械革命 极光 X 2025 / Mechrevo Aurora X 2025** 的 OpenCore EFI
配置。当前配置由谢清宇工作室在本机导出、脱敏并整理。

> 当前状态：系统可以正常启动，键盘、显示、网络、蓝牙、音频、电池和 USB
> 可用；**内置触控板仍无法驱动**。这不是一份通用黑苹果 EFI，请仅在相同或
> 高度接近的硬件上测试。

## 验证信息

| 项目 | 信息 |
| --- | --- |
| 机型 | 机械革命 极光 X 2025 |
| CPU | Intel Core i7-13700HX，16 核 24 线程 |
| 独立显卡 | NVIDIA GeForce RTX 5060 Laptop GPU，8 GB |
| 内存 | 16 GB |
| 内置屏幕 | 2560 x 1600，180 Hz |
| 硬盘 | KINGSTON OM8TAP41024K1-A00，1 TB NVMe |
| 无线网卡 | Intel Wi-Fi 6 AX201 |
| 有线网卡 | Realtek RTL8125B 2.5 GbE |
| 声卡 | Conexant，通过 VoodooHDA 驱动 |
| macOS | 15.8.1 / 24H32 |
| OpenCore | `DBG-109-2026-10-17` |
| SIP | 已关闭 |
| 验证日期 | 2026-10-20 |

`config.plist` 已通过 OpenCore 1.0.8 的 `ocvalidate` 检查。

## 功能状态

### 已验证可用

| 功能 | 说明 |
| --- | --- |
| 启动 | OpenCore 可正常进入 macOS |
| 内置显示 | 2560 x 1600，180 Hz；当前使用 HiDPI 缩放 |
| 显卡 | 当前系统已安装 NullMoth NVIDIA 内核扩展并工作 |
| Wi-Fi | Intel AX201，使用 `AirportItlwm_Sequoia.kext` 2.4.0 |
| 蓝牙 | Intel 蓝牙，使用 `IntelBluetoothFirmware.kext` 2.5.0 |
| 有线网络 | Realtek RTL8125B，使用 `RTL812xLucy.kext` 1.1.100 |
| 音频 | Conexant 扬声器与麦克风，使用 `VoodooHDA_BL.kext` 3.7.0 |
| 键盘 | PS/2 键盘正常 |
| 电池 | `SMCBatteryManager.kext` 可读取电池状态 |
| USB | USBToolBox 注入配置可用 |
| 摄像头 | 内置 UVC 摄像头可识别 |

### 已知问题

内置 I2C 触控板无法驱动。当前 `VoodooI2C.kext` 和
`VoodooI2CHID.kext` 已启用，启动参数也包含 `-vi2c-force-polling`，但系统
中只检测到 I2C 控制器和 `TPAD` 设备节点，没有生成可用的触控板 HID 设备。

本机观察到的设备信息：

```text
PCI 路径:    PciRoot(0x0)/Pci(0x15,0x0)
PCI 兼容 ID: pci1d05,5002 / pci8086,7a4c
ACPI 路径:   _SB.PC00.I2C0.TPAD
GPIO 控制器: GPI0 / INTC1085
```

欢迎针对该触控板提交 DSDT、`ioreg` 和内核日志，但不要提交机器序列号、
MLB、硬件 UUID、Wi-Fi MAC 或 Windows 激活信息。

## 重要限制

### 显卡补丁不在 EFI 中

当前显卡加速依赖安装在 macOS 系统卷侧 `/Library/Extensions` 下的 NullMoth
NVIDIA 扩展：

```text
NVRM.kext
NVRMAGDC.kext
NVRMFB.kext
NVAccel.kext
```

这些内核扩展不属于 EFI，也没有包含在本仓库中。仅复制本 EFI 到一台全新安装的
系统，不能自动获得相同的 NVIDIA 显卡加速。请先按照 NullMoth 上游说明完成系统
侧补丁，再测试本 EFI。

### SMBIOS 已脱敏

公开版 `config.plist` 中的以下字段已清空：

```text
PlatformInfo > Generic > SystemSerialNumber
PlatformInfo > Generic > MLB
PlatformInfo > Generic > SystemUUID
PlatformInfo > Generic > ROM
```

使用前必须使用 GenSMBIOS 或你自己的有效 SMBIOS 数据填写这些字段。机型仍为
`MacBookPro16,2`，`ProcessorType` 为 `3841`。不要直接使用其他人的整套
SMBIOS 数据，也不要把自己的序列号再次提交到公开仓库。

### 安全设置

本配置使用：

```text
SecureBootModel = Disabled
csr-active-config = 0x0C
boot-args = -v debug=0x100 keepsyms=1 -vi2c-force-polling ctrsmt=full
            nvfb=1 nvaccel=1 nvfbheads=4 -nvkmsnosmooth amfi=0x80
            -amfipassbeta ipc_control_port_options=0 gfxrst=4
            nvrmacpibl=1
```

这适合当前黑苹果环境和调试阶段，不适合对安全启动有要求的生产系统。

## 安装

### 1. 备份现有 EFI

先完整备份当前可启动的 EFI。不要把本目录直接覆盖到唯一一份 EFI 上。

### 2. 准备 EFI 分区

准备一个 FAT32 分区或 U 盘 EFI 分区，建议容量不少于 200 MB。将本仓库中的
`EFI` 目录放到分区根目录，最终结构应为：

```text
EFI/
├── BOOT/
│   └── BOOTx64.efi
└── OC/
    ├── ACPI/
    ├── Drivers/
    ├── Kexts/
    ├── Resources/
    ├── Tools/
    ├── OpenCore.efi
    └── config.plist
```

### 3. 生成 SMBIOS

使用 GenSMBIOS 选择 `MacBookPro16,2`，生成并填写：

- `SystemSerialNumber`
- `MLB`
- `SystemUUID`
- `ROM`，填写 6 字节本机网卡 MAC，不加冒号

填写后再次保存为 plist，并运行 OpenCore 对应版本的 `ocvalidate`。

### 4. 从 U 盘测试

先在 BIOS 中选择 U 盘启动，确认能够进入 OpenCore 和 macOS。不要一开始就覆盖
内置硬盘 EFI。

### 5. 安装系统侧补丁

如果是全新安装，先完成 macOS 安装，再按照 NullMoth 的说明安装 NVIDIA 系统
侧扩展。EFI 本身不包含这部分补丁。

### 6. 迁移到内置硬盘

U 盘启动、触控板以外的功能确认正常后，再把 EFI 复制到内置硬盘 EFI 分区，并在
BIOS 中把对应启动项设为第一启动项。

## 目录内容

### ACPI

```text
SSDT-EC.aml
SSDT-GPI0.aml
SSDT-MCHC.aml
SSDT-PLUG-ALT.aml
SSDT-RTCAWAC.aml
SSDT-SBUS.aml
SSDT-USB-Reset.aml
SSDT-USBX.aml
SSDT-XOSI.aml
```

### OpenCore 驱动

```text
HfsPlus.efi
OpenCanopy.efi
OpenRuntime.efi
ResetNvramEntry.efi
```

### 内核扩展

| Kext | 版本 |
| --- | ---: |
| Lilu | 1.7.3 |
| VirtualSMC | 1.3.9 |
| SMCBatteryManager | 1.3.9 |
| SMCLightSensor | 1.3.9 |
| SMCProcessor | 1.3.9 |
| SMCSuperIO | 1.3.9 |
| WhateverGreen | 1.7.2 |
| NVMeFix | 1.1.4 |
| RestrictEvents | 1.1.7 |
| BrightnessKeys | 1.0.4 |
| AMFIPass | 1.4.1 |
| CpuTopologyRebuild | 2.0.2 |
| AirportItlwm_Sequoia | 2.4.0 |
| IntelBluetoothFirmware | 2.5.0 |
| IntelBTPatcher | 2.5.0 |
| BlueToolFixup | 2.7.3 |
| RTL812xLucy | 1.1.100 |
| VoodooHDA_BL | 3.7.0 |
| VoodooI2C | 2.9.1 |
| VoodooI2CHID | 1 |
| VoodooPS2Controller | 2.3.8 |
| USBToolBox | 1.2.0 |
| UTBDefault | 1.0 |
| XHCI-unsupported | 0.9.2 |

### 工具

```text
NullMothSafe.efi
```

该工具用于移除或临时跳过 NVIDIA 驱动，属于当前机器调试流程的一部分。使用前请
确认其来源和版本。

## 隐私与发布范围

公开包不包含：

- `EFI/Microsoft` 下的 Windows 引导文件；
- EFI 根目录下的 OpenCore 启动日志；
- `config.plist.xieqingyu-*` 和 `CONFI~29.NU0` 配置备份；
- macOS Spotlight、Trash、fseventsd 等卷元数据；
- 原始 `SystemSerialNumber`、`MLB`、`SystemUUID`、`ROM`；
- 本机 Wi-Fi 和蓝牙 MAC 地址。

下载压缩包后，请先核对 `SHA256SUMS.txt`，再填写自己的 SMBIOS。

## 第三方组件

本仓库原创的文档、配置整理和适配内容使用
[GNU General Public License v3.0](LICENSE) 授权。

OpenCore、内核扩展、驱动和工具是第三方组件，分别遵循各自上游项目的许可证；
GPLv3 不会替代或修改这些上游许可证。组件来源与许可证入口见
[THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md)。

## 致谢

感谢 OpenCorePkg、Acidanthera、OpenIntelWireless、VoodooI2C、Mieze、
USBToolBox 以及相关内核扩展的维护者。该 EFI 的机型适配、主题和导出整理由
谢清宇工作室完成。

## 免责声明

黑苹果配置存在损坏引导、无法启动、数据丢失和系统不稳定的风险。请先备份数据
和现有 EFI，并在可恢复的环境中进行测试。因使用本配置造成的任何损失，作者不
承担责任。
