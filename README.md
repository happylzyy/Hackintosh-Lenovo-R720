Hackintosh-Lenovo-R720
=========

适用于联想拯救者R720-15IKBN OpenCore EFI，经过测试可以用于

- macOS Ventura 13.x
- macOS Monterey 12.x
- macOS Big Sur 11.x
- macOS Catalina 10.15.x

macOS Sonoma 或许可以使用，但没有测试，若要安装需要手动修改config替换成Sonoma支持的smbios机型。

macOS Sonoma 移除了对Broadcom无线网卡的支持，驱动方法请看[macOS-Sonoma-Broadcom-Wifi](https://github.com/billabongbruno/macOS-Sonoma-Broadcom-Wifi)，关于Intel无线网卡的驱动请看[OpenIntelWireless](https://openintelwireless.github.io/)。

#### 配置信息

| 项目                    | 参数                             |
| ----------------------- | -------------------------------- |
| 电脑型号                | Lenovo Rescuer R720-15IKBN       |
| CPU                     | Intel Core i5-7300HQ (Kaby Lake) |
| 显卡                    | 核显 HD 630；独显 GTX 1050 Ti    |
| 有线网卡                | Realtek RTL8168GU/8111GU         |
| 无线网卡/蓝牙（已更换） | BCM94360CS2                      |
| 声卡                    | Realtek ALC235                   |

#### 已驱动

* 核显 HD630正常驱动，独显已屏蔽

* 屏幕亮度调节正常，亮度调节快捷键<kbd>Fn+F11</kbd>和<kbd>Fn+F12</kbd>正常驱动

* CPU变频正常

  支持900MHz～3100MHz共23档变频（来自CPU-S工具的估算值，实际睿频看情况）

* 睡眠唤醒正常

  普通睡眠、合盖睡眠都正常

* 声卡驱动正常（layout-id=28）

  扬声器、耳机正常，麦克风阵列、线路输入正常，EAPD工作正常

* 有线网卡驱动正常

* 支持苹果触控板手势，小键盘可正常使用，NumLock键正常

* 摄像头驱动正常

* USB2.0/3.0，type-C驱动正常

* 蓝牙（更换无线网卡）

  随航、接力、隔空投送、通用控制都可以正常使用

* WLAN（更换无线网卡）

* 联想OEM的部分功能，经过测试<kbd>Fn+Fxx</kbd>键可以使用、风扇传感器正常、电池温度传感器正常

#### 存在问题

* macOS版本Ventura及以上的HDMI不可用，因为旧smbios机型不再支持，可以装旧版本macOS通过改机型（比如MacbookPro11,4）打开HDMI

* SD读卡器不能正常驱动

#### 使用

1. 复制BOOT和OC文件夹至ESP分区下的EFI文件夹；
2. 修改`config.plist>Platforminfo>Generic`，在`MLB`、`ROM`、`SystemSerialNumber`、`SystemUUID`填入自己生成的设备信息；
4. 修改添加UEFI启动项，从OC启动；
4. 尝试进入macOS系统。

#### 注意事项

1. <kbd>PrtSc</kbd>键已通过SSDT-PS2K映射到<kbd>F13</kbd>，在系统偏好设置中设置截图快捷键为<kbd>F13</kbd>可以使用系统自带截图功能。

2. 添加了YogaSMC用来驱动联想OEM部分功能，比如Fn+Fxx键、键盘背光控制、风扇传感器，功能的启用需要安装额外的应用和面板[YogaSMCNC](https://github.com/zhen-zen/YogaSMC/releases)。

3. 触控板三指拖移手势（比如拖动文件、选择文字，Windows下是双击拖动）需要到“设置->辅助功能->指针控制->触控板选项->启用拖移->三指拖移”开启，其他手势在“设置->触控板”里设置。

4. OpenCore通过修改操作系统的运行环境以使其更适合macOS（比如屏蔽独显，修改序列号，修改硬件UUID，修改DSDT等），这种修改可能对其他操作系统产生不利影响，比如win下的独立显卡消失、部分软件激活失败，因此不建议使用OpenCore引导Windows等其他操作系统。

5. 解锁MSR 0x2E CFG Lock后可以加载苹果的原生电源管理，理论上更省电，更接近白苹果，详见[MSR_CFG_unlock.md](https://github.com/happylzyy/Hackintosh-Lenovo-R720/blob/main/MSR_CFG_unlock.md)。

6. 自己定制睡眠模式，可以进一步优化macOS的电源管理，定制睡眠模式需要用到`pmset`命令，具体说明和用法请见[详解Mac睡眠模式设置](https://www.cnblogs.com/motoyang/p/4947139.html)和[Mac的睡眠模式介绍](https://www.cnblogs.com/motoyang/p/6075609.html)。

   举个例子：

   ```bash
   ❯ pmset -g custom #显示所有模式下的电源管理参数
   Battery Power:		#电池状态下的电源管理
    lowpowermode         1
    autopoweroff         1
    standbydelayhigh     86400
    autopoweroffdelay    259200
    proximitywake        0
    standby              1
    standbydelaylow      10800
    ttyskeepawake        1
    hibernatemode        3		#睡眠模式3
   ...
   AC Power:					#插电源状态下的电源管理
    autopoweroff         1
    lowpowermode         0
    standbydelayhigh     86400
    autopoweroffdelay    259200
    proximitywake        1
    standby              1
    standbydelaylow      10800
    ttyskeepawake        1
    hibernatemode        0		#睡眠模式0
   ...
   ```

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

   感谢为这些项目和教程作出贡献的大佬们！
