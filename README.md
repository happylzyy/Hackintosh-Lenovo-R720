Hackintosh-Lenovo-R720
=========

联想拯救者R720-15IKBN OpenCore引导，经过测试可以用于

- macOS Monterey 12.1
- macOS Monterey 12.0.1
- macOS Big Sur 11.x
- macOS Catalina 10.15.x

#### 配置信息

| 项目                    | 参数                             |
| ----------------------- | -------------------------------- |
| 电脑型号                | Lenovo Rescuer R720-15IKBN       |
| CPU                     | Intel Core i5-7300HQ (Kaby Lake) |
| 显卡                    | 核显 HD 630；独显 GTX 1050 Ti    |
| 有线网卡                | Realtek RTL8168GU/8111GU         |
| 无线网卡/蓝牙（已更换） | BCM94360CS2                      |
| 声卡                    | Realtek ALC235                   |

#### 驱动

* 核显 HD630 Lilu+WhateverGreen+Device Properties注入ID

* 屏幕亮度调节正常，亮度控制键<kbd>Fn+F11</kbd>和<kbd>Fn+F12</kbd>正常

* CPU变频正常 CPUFriend + CPUFriendDataProvider

  支持900MHz～3000MHz共22档变频（来自CPU-S工具估算值，实际睿频看情况）

* 声卡 AppleALC + CodecCommander

  layout-id=28

  扬声器、耳机正常，麦克风阵列、线路输入正常，EAPD工作正常

* 有线网卡 RealtekRTL8111.kext

* 触控板手势全部可用，小键盘正常 VoodooPS2Controller

* 摄像头可用

* USB2.0/3.0 USBToolBox定制方案

* 蓝牙（更换无线网卡）

  随航正常、接力正常、隔空投送正常

* WLAN（更换无线网卡）

* 睡眠唤醒正常

#### 存在问题

* HDMI不能用，SMBIOS机型换成带HDMI接口的机型可以输出，但没有声音

* <kbd>PrtSc</kbd>键通过SSDT-PS2K映射到F13，在系统偏好设置中设置快捷键可以使用系统自带截图功能

* 电池没有热补丁，但是不影响电池正常使用和电量显示

#### 使用

1. 复制BOOT和OC文件夹至ESP分区下的EFI文件夹；
2. 修改`config.plist>Platforminfo>Generic`，在`MLB`、`ROM`、`SystemSerialNumber`、`SystemUUID`填入自己生成的设备信息；
4. 修改UEFI启动项，从OC启动；
4. 出现问题可参考下一节注意事项。

#### 注意事项

1. 对于macOS Big Sur 和macOS Catalina，安装和升级的过程中可能卡`IOG Flags 0x3 (0x51)`，解决方法是在`Config.plist>DeviceProperties>Add>PciRoot(0x0)/Pci(0x2,0x0)>APPL,ig-platform-id`中填入`12345678`，待安装或升级成功后再改回原来值。
2. 对于macOS Monterey，
   - 从旧版本升级可能需要额外的boot-args，包括`vsmcgen=1 -lilubetaall vsmcbeta`。
   - 如果从旧版本更新时出现循环重启，需要将`Config.plist>Misc>Security>SecureBootModel`设置为`Disabled`，**重新下载更新**再安装。
3. 开机声音默认开启，如不需要，可以在`Config.plist>UEFI>Audio>AudioSupport`中禁用它。

#### 高阶操作

解锁MSR 0x2E CFG Lock使用苹果原生电源管理，详情请见[MSR_CFG_unlock.md](https://github.com/happylzyy/Hackintosh-Lenovo-R720/blob/main/MSR_CFG_unlock.md)。

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

   感谢为这些项目作出贡献的大佬们！

#### 更新日志

>2022.01.09
>
>- 添加HibernationFixup.kext来支持休眠模式3和25
>- 修复了`wakeup reason: GLAN XHCI` 问题
>- 修复了亮度控制快捷键<kbd>Fn+F11</kbd>和<kbd>Fn+F12</kbd>不能用的问题

>2022.02.08
>
>- 修复屏幕少量雪花的问题
