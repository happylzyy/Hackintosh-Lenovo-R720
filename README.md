Hackintosh-Lenovo-R720
=========

#### 配置信息

| 项目                    | 参数                             |
| ----------------------- | -------------------------------- |
| 电脑型号                | Lenovo Rescuer R720-15IKBN       |
| CPU                     | Intel Core i5-7300HQ (Kaby Lake) |
| 显卡                    | 核显 HD 630；独显 GTX 1050 Ti    |
| 有线网卡                | Realtek RTL8168GU/8111GU         |
| 无线网卡/蓝牙（已更换） | BCM94360CS2                      |
| 声卡                    | Realtek ALC235                   |

适用于联想拯救者R720-15IKBN OpenCore EFI，经过测试可以用于

- macOS Sequoia 15.0
- macOS Ventura 13.x
- macOS Monterey 12.x
- macOS Big Sur 11.x
- macOS Catalina 10.15.x

旧版本macOS不支持直接升级到macOS Sonoma / Sequoia，需要使用[OpenCore Legacy Patcher](https://dortania.github.io/OpenCore-Legacy-Patcher/)制作修补后的U盘镜像升级。用本EFI替换U盘EFI，从U盘启动升级。

macOS Sonoma / Sequoia 移除了Broadcom、Atheros等无线网卡的驱动，需要使用[OpenCore Legacy Patcher](https://dortania.github.io/OpenCore-Legacy-Patcher/)对系统进行修补。

关于Intel无线网卡的驱动见[OpenIntelWireless](https://openintelwireless.github.io/)。

#### 已驱动

* 核显 HD630

* 屏幕亮度调节，亮度调节功能键

* CPU变频：支持900MHz～3100MHz共23档变频（来自CPU-S工具的估算值）

* 睡眠唤醒：普通睡眠、合盖睡眠正常

* 声卡驱动（layout-id=14）：扬声器、耳机、麦克风阵列、线路输入、EAPD工作正常

* 有线网卡

* 触控板与键盘：支持苹果触控板手势，小键盘与NumLock键正常

* 摄像头

* USB2.0/3.0，type-C

* 蓝牙（BCM94360CS2）：随航、接力、隔空投送、通用控制

* WLAN（BCM94360CS2）

* 联想OEM的部分功能：<kbd>Fn+Fxx</kbd>键、风扇传感器、电池传感器

* SD读卡器

* HDMI、type-C外接显示器

#### 已知问题

* HDMI、type-C外接显示器分辨率识别可能不准确


#### 附加内容

1. <kbd>PrtSc</kbd>键已通过SSDT-PS2K映射到<kbd>F13</kbd>，系统偏好设置中设置截图快捷键为<kbd>F13</kbd>即可使用PrtSc截图功能。
2. 联想OEM部分功能（Fn+Fxx键、键盘背光控制、风扇传感器）需要安装额外的应用和面板[YogaSMCNC](https://github.com/zhen-zen/YogaSMC/releases)。
3. 关于解锁MSR 0x2E CFG Lock等BIOS修改，详见[MSR_CFG_unlock.md](https://github.com/happylzyy/Hackintosh-Lenovo-R720/blob/main/MSR_CFG_unlock.md)和[BIOS variable](https://github.com/happylzyy/Hackintosh-Lenovo-R720/blob/main/bios_variable.txt)。

#### 参考资料

1. [英特尔® 核芯显卡 常见问答](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.cn.md)
2. [AppleALC](https://github.com/acidanthera/AppleALC)
3. [VoodooPS2](https://github.com/acidanthera/VoodooPS2)
4. [OC-little](https://github.com/daliansky/OC-little)
5. [OpenCore黑苹果引导开机声音与图形界面设置](https://shuiyunxc.gitee.io/2020/03/19/SoundGra/index/)
6. [AirportBrcmFixup](https://github.com/acidanthera/AirportBrcmFixup)
7. [USBToolBox](https://github.com/USBToolBox)
8. [EAPD-Codec-Commander](https://github.com/RehabMan/EAPD-Codec-Commander)
9. [CPUFriend](https://github.com/acidanthera/CPUFriend)
10. [one-key-cpufriend](https://github.com/stevezhengshiqi/one-key-cpufriend)
11. [详解Mac睡眠模式设置](https://www.cnblogs.com/motoyang/p/4947139.html)、[Mac的睡眠模式介绍](https://www.cnblogs.com/motoyang/p/6075609.html)
12. [YogaSMC](https://github.com/zhen-zen/YogaSMC)
13. [OpenCore Legacy Patcher](https://dortania.github.io/OpenCore-Legacy-Patcher/)
14. [ferxiit/Y520-15IKBN-OpenCore](https://github.com/ferxiit/Y520-15IKBN-OpenCore)
15. [[Guide] Lenovo Legion Y520/Y720 using Clover UEFI](https://www.tonymacx86.com/threads/guide-lenovo-legion-y520-y720-using-clover-uefi.261009/)
