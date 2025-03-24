# oc-hp-envy13
# HP Envy 13 黑苹果 EFI 文件说明

## 目录

- [配置](#配置)
- [运行情况](#运行情况)
- [EFI 文件结构](#efi-文件结构)

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

