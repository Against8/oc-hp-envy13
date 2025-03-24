
# HP Envy 13 黑苹果 EFI 文件说明

## 目录

- [配置](#配置)
- [运行情况](#运行情况)
- [EFI 文件结构](#efi-文件结构)
- [Sonoma 以上系统说明](#Sonoma-以上系统说明)
## 配置

| 组件   | 型号        |
|--------|-------------|
| CPU    | Intel Core i5-8265U |
| 网卡   | Intel Dual Band Wireless-AC 94360CS2 |
| 硬盘   | 金士顿 A2000 1TB |

## 运行情况

- MacOS 13以上均可安装运行
- 麦克风无法使用(英特尔智声暂时没有驱动解决)
- 其他功能均正常运行


## EFI 文件结构

```
EFI
├── CLOVER
│   ├── ACPI
│   ├── drivers
│   ├── kexts
│   ├── config.plist
│   └── tools
└── BOOT
```

## Sonoma 以上系统说明

由于Apple在Sonoma中删除了老博通免驱网卡的驱动,因此需要OCLT来重新注入驱动,具体步骤:


### 1. 禁止 AMFI

- **配置项**: `NVRAM>Add>UUID>boot-args|amfi=0x80`
  - **UUID**: `7C436110-AB2A-4BBB-A880-FE41995C9F82`
  - **注意**: 不再建议使用 `amfi=0x80`，请注入以下 kext:
    - **AMFIPass.kext**
    - **启动参数**: `NVRAM>Add>UUID>boot-args|-amfipassbeta`
    - **更新**: AMFIPass.kext 更新到 1.4.0 版本可不需要使用该启动参数
    - **MinKernel**: 可设置为 `23.0.0` 仅在 Sonoma 生效，也可不设置

### 2. 设置系统完整性保护

- **配置项**: 将系统完整性保护设置为 `0x0803`
  - **操作**: 开机请重置 NVRAM 或将 `csr-active-config` 添加到删除：
    - `NVRAM>Add>UUID>csr-active-config | 03080000`
    - `NVRAM>Delete>UUID>csr-active-config`
  - **数据类型**
  - **UUID**: `7C436110-AB2A-4BBB-A880-FE41995C9F82`

### 3. 禁止安全启动模型

- **配置项**: `Misc>Security>SecureBootModel: Disabled`

### 4. 阻止加载的 kext

- **kext**: `com.apple.iokit.IOSkywalkFamily`
  - **MinKernel**: 可设置为 `23.0.0` 仅在 Sonoma 生效，也可不设置
  - **策略**: 请将其 `Strategy` 设置为 `Exclude`

### 5. kext 注入顺序

- **顺序**:
  1. `IOSkywalkFamily.kext`
  2. `IO80211FamilyLegacy.kext`
     - **注意**: 不要试图注入系统自带的 `IOSkywalkFamily.kext`
     - **嵌套链接**: `IO80211FamilyLegacy.kext>AirPortBrcmNIC.kext`
     - **单独注入**: AirPortBrcmNIC.kext 可从 `IO80211FamilyLegacy.kext` 中提取出来单独注入
  - **MinKernel**: 可设置为 `23.0.0` 仅在 Sonoma 生效，也可不设置

### 6. 安装 OpenCore Legacy Patcher (OCLP)

  1. 正确完成以上所有设置后，请重启电脑
  2. 安装 OCLP, 再次重启电脑
     - **路径**: `OCLP.app>Post-Install Root Patch>Start Root Patching>Reboot`
  3. 重启电脑后，应该已恢复无线网络支持
  - **启动参数**:
    1. `revpatch=sbvmm`（须配合使用内核事件修补程序 `RestrictEvents.kext`）
    2. `ipc_control_port_options=0`
