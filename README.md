Hackintosh-Lenovo-R720
=========

联想拯救者R720-15IKBN OpenCore引导，适用于macOS Big Sur 11.0.1

#### 配置信息

| 项目               | 参数                             |
| ------------------ | -------------------------------- |
| 电脑型号           | Lenovo Rescuer R720-15IKBN       |
| CPU                | Intel Core i5-7300HQ (Kaby Lake) |
| 显卡               | 核显 HD 630；独显 GTX 1050 Ti    |
| 有线网卡           | Realtek RTL8168GU/8111GU         |
| 无线网卡（已更换） | BCM94360CS2                      |
| 声卡               | Realtek ALC235                   |

#### 驱动

* 核显 HD630 Lilu+WhateverGreen+Device Properties注入ID

* CPU变频正常

* 声卡 AppleALC layout-id=28

  扬声器、耳机正常，麦克风阵列、线路输入正常

* 有线网卡 RealtekRTL8111.kext

* 触控板手势全部可用 新版VoodooPS2Controller

* 摄像头可用

* USB2.0/3.0

* 蓝牙（更换无线网卡）

* WLAN（更换无线网卡）

* 睡眠唤醒正常

#### 存在问题

* 合盖后唤醒有几率系统崩溃，错误报告已经保存，以后尝试修复
* HDMI不能用，SMBIOS机型换成带HDMI接口的机型可以输出，但没有声音
* 屏幕显示有极少数的雪花一闪而过
* 小键盘不能用
* 亮度控制键<kbd>Fn+F11</kbd>和<kbd>Fn+F12</kbd>不能用，据说可以通过DSDT修复，还没搞出来

#### 使用

1. 复制BOOT和OC文件夹至ESP分区下的EFI文件夹
2. 修改`config.plist>Platforminfo>Generic`，在`MLB`、`ROM`、`SystemSerialNumber`、`SystemUUID`填入自己的机器信息；
3. 修改UEFI设置，从OC启动。

#### 注意事项

1. 安装和升级的过程中可能卡`IOG Flags 0x3 (0x51)`，在`Config.plist>DeviceProperties>Add>PciRoot(0x0)/Pci(0x2,0x0)>APPL,ig-platform-id`中填入`12345678`可以解决，待安装或升级成功后再改回原来值。
2. 如果启动菜单无法识别安装盘，删除`Config.plist>Misc>Security>ScanPolicy`中的值。

#### 参考资料

1. [英特尔® 核芯显卡 常见问答](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.cn.md)
2. [AppleALC](https://github.com/acidanthera/AppleALC)
3. [VoodooPS2](https://github.com/acidanthera/VoodooPS2)
4. [OC-little](https://github.com/daliansky/OC-little)
5. QQ群 联想拯救者R720黑苹果 494816700

