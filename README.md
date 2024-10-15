Hackintosh-Lenovo-R720
=========

联想拯救者R720-15IKBN OpenCore引导，经过测试可以用于

- macOS Big Sur 11.2.1

- macOS Big Sur 11.1
- macOS Big Sur 11.0.1
- macOS Catalina 10.15.x

#### 配置信息

| 项目                    | 参数                                       |
| ----------------------- | ------------------------------------------ |
| 电脑型号                | Lenovo Rescuer R720-15IKBN                 |
| CPU                     | Intel Core i5-7300HQ (Kaby Lake)           |
| 显卡                    | 核显 HD 630；独显 GTX 1050 Ti              |
| 有线网卡                | Realtek RTL8168GU/8111GU                   |
| 无线网卡/蓝牙（已更换） | BCM94360CS2，可选启用BCM94352z(DW1560)驱动 |
| 声卡                    | Realtek ALC235                             |

#### 驱动

* 核显 HD630 Lilu+WhateverGreen+Device Properties注入ID

* CPU变频正常

* 声卡 AppleALC layout-id=28

  扬声器、耳机正常，麦克风阵列、线路输入正常

* 有线网卡 RealtekRTL8111.kext

* 触控板手势全部可用 新版VoodooPS2Controller

* 摄像头可用

* USB2.0/3.0 SSDT-UIAC+SSDT-USBX+USBinjectAll

* 蓝牙（更换无线网卡）

  随航正常、接力正常、隔空投送正常

* WLAN（更换无线网卡）

* 睡眠唤醒正常

#### 存在问题

* ~~合盖后唤醒有小概率系统崩溃错误，错误报告已经保存，以后尝试修复~~

  关闭系统偏好设置中`唤醒以供网络访问`可以避免系统崩溃

* HDMI不能用，SMBIOS机型换成带HDMI接口的机型可以输出，但没有声音

* 屏幕显示有极少数的雪花一闪而过

* ~~小键盘和触控板问题见[issues#1](https://github.com/happylzyy/Hackintosh-Lenovo-R720/issues/1)~~

* <kbd>PrtSc</kbd>键通过SSDT映射到F13，在系统偏好设置中设置快捷键以使用截图功能。

* 亮度控制键<kbd>Fn+F11</kbd>和<kbd>Fn+F12</kbd>不能用，据说可以通过DSDT修复，还没搞出来

  使用小键盘<kbd>4</kbd>和<kbd>-</kbd>键分别控制亮度减和亮度加
  
* 电池供电时，耳机声音沉闷，可能通过定制正确的AppleALC layout解决

  当电池供电时，外部放大器 (External Amplifier, EAPD) 关闭耳机麦克风以节省电能，影响到声音输出

* 电池没有热补丁，但是不影响电池正常使用和电量显示

#### 使用

1. 复制BOOT和OC文件夹至ESP分区下的EFI文件夹；
2. 修改`config.plist>Platforminfo>Generic`，在`MLB`、`ROM`、`SystemSerialNumber`、`SystemUUID`填入自己的机器信息；
3. 查看下一节注意事项，进行相关修改；
4. 修改UEFI设置，从OC启动。

#### 注意事项

1. 安装和升级的过程中可能卡`IOG Flags 0x3 (0x51)`，在`Config.plist>DeviceProperties>Add>PciRoot(0x0)/Pci(0x2,0x0)>APPL,ig-platform-id`中填入`12345678`可以解决，待安装或升级成功后再改回原来值。
2. EFI默认支持开机音频，在`Config.plist>UEFI>Audio>AudioSupport`中可以禁用它。
3. 添加了BCM94352z(DW1560)驱动，如有需要可在`Config.plist>Kernel>Add`中将驱动勾选启用，不确定有没有用。
4. ~~参考[issues#1](https://github.com/happylzyy/Hackintosh-Lenovo-R720/issues/1)选择键盘驱动。~~

#### 参考资料

1. [英特尔® 核芯显卡 常见问答](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.cn.md)

2. [AppleALC](https://github.com/acidanthera/AppleALC)

3. [VoodooPS2](https://github.com/acidanthera/VoodooPS2)

4. [OC-little](https://github.com/daliansky/OC-little)

5. [OpenCore黑苹果引导开机声音与图形界面设置](https://shuiyunxc.gitee.io/2020/03/19/SoundGra/index/)

6. [AirportBrcmFixup](https://github.com/acidanthera/AirportBrcmFixup)

   感谢为这些项目作出贡献的大佬们！

#### 更新日志

> 2020.11.19
>
> * 增加UEFI开机音频支持
> * 增加开机系统选择菜单主题
> * 删除多余的Drivers，因为OC已经内置
> * 增加PrtSc到F13映射

>2020.11.23
>
>- 删除自定义的PCI设备信息，避免加各类网卡驱动时产生冲突
>- 添加BCM94352z(DW1560)驱动，**未测试，默认禁用**

>2020.12.19
>
>- 更新OpenCore至0.6.4版本
>- 更新VoodooPS2Controller至2.1.9版本

> 2021.03.02
>
> - 添加最新编译的VoodooPS2驱动，解决了小键盘不能用的问题
> - 删除了旧驱动ApplePS2SmartTouchPad