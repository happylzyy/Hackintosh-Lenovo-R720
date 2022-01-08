### R720-15IKBN MSR 0xE2 CFG Lock 解锁

===============

#### 什么是 MSR CFG Lock 解锁？

MSR（Model Specific Register）是x86架构中的概念，指的是在x86架构处理器中，一系列用于控制CPU运行、功能开关、调试、跟踪程序执行、监测CPU性能等方面的寄存器。

大多数UEFI主板厂家，锁定了MSR寄存器的第15位为只读，也就是MSR 0xE2 Locking（BIOS 中叫CFG Lock）。苹果macOS系统的电源管理、CPU的P-state、C-state放在MSR寄存器里，MSR 0xE2被锁定为只读后，macOS系统的电源管理驱动扩展AppleIntelCPUPowerManagement一旦尝试去写入数据，系统就会马上崩溃。有些主板上有选项“CFG Lock”，其说明内容为”关闭或开启MSR 0xE2″，可以手动开关，有些主板则屏蔽了这个选项。当使用黑苹果时，必须解锁MSR 0xE2，否则无法使用苹果的原生电源管理。

联想等一些 OEM 设置了BIOS内部的变量，也就是说固化到了BIOS的固件代码里，如果不对 BIOS 进行物理修改就无法解锁它，对于这种情况，需要修改BIOS才能解锁CFG Lock。Lenovo R720-15IKBN就属于上述情况，因此要对BIOS进行修改。

**刷机有风险，搞机须谨慎！失败会造成电脑变砖，没有救砖技术请不要尝试！本文仅供参考，对于读者自己造成的损坏不承担任何责任！**

#### 环境准备

- **插了稳定交流电源**的Lenovo R720-15IKBN 笔记本电脑
- Windows 10，因为大部分操作都在Win环境下进行
- macOS与OpenCore
- 一个格式化文件系统为FAT32的U盘
- 安静的没有人打扰您的环境

#### 工具准备

- Lenovo 官方 `4KCN27WW`版 BIOS

  联想官方BIOS通常具有写保护措施，而27版BIOS没有写保护因此可以进行修改，BIOS降级程序可在官网输入主机编号后寻找下载到。

  下载地址：https://newsupport.lenovo.com.cn/

- Intel CSME System Tools

  用于Dump BIOS 固件，根据不同的平台选择不同的版本，这里选择v11 r37版。

  下载地址：https://comsystem-tlt.ru/obzori/me-txe-region

- UEFITool

  查找BIOS中相关变量（字符串）的工具。

  下载地址：https://github.com/LongSoft/UEFITool/releases

- IRFExtractor

  将UEFITool得到的PE32 image转换成文本的工具。

  下载地址：https://github.com/LongSoft/Universal-IFR-Extractor/releases

- easyUEFI或Bootice

  用于修改UEFI启动项。

#### BIOS 降级

进入UEFI BIOS 设置，将BIOS Back Flash设为Enabled，允许BIOS回滚刷新；

重启进入Windows，运行从官网下载的27版BIOS降级程序，安装完成后系统自动重启，开始降级过程。 **注意在降级过程中一定不要断开电源！** BIOS刷写成功后，电脑会自动重启数次，属于正常现象，不要断电源。

刷写后可能无法进入Win系统，重新进入UEFI BIOS设置，关闭安全启动，将SATA控制器工作模式设置为AHCI，保存设置后重启就能进入系统。

#### 提取主板BIOS

进入系统后，打开`Intel CSME System Tools v11 r37/Flash Programming Tool/WIN64`，新建文本文档，输入以下代码

```shell
cd /d %~dp0
fptw64.exe -bios -d backup.fd
```

保存后修改文件扩展名为`.cmd`，右键以管理员身份运行，提取主板BIOS，提取后的文件存储在同目录下，名字为`backup.fd`。

#### 提取BIOS信息

打开UEFITool，菜单栏选择`File > Open image file...`，选择上一步提取出的`backup.fd`。

菜单栏选择`Action > Search...`，在弹出的对话框中选择`Text`，输入`CFG Lock`，搜索的结果在下方显示，双击`Unicode text "CFG Lock" in DriverSampleDxe/PE32 image section at header-offset 496F4h`定位到CFG Lock所在位置。

在定位到的位置右击`PE32 image section`，选择`Extarct as is...`，将PE32 image文件保存。

接下来，我们需要把PE32 image转换成我们能读懂的信息。

打开IFRExtractor，直接点击右侧加载按钮，打开上一步的PE32文件，再点击Extract，就会生成txt文本，并保存到相应的文件夹。

#### 寻找需要的偏移量

打开转换的txt文件，搜索字符串`CFG Lock`，定位到CFG Lock位置，如下所示，不同机器可能不同：

```assembly
0x9B0B4 	Form: View/Configure CPU Lock Options, FormId: 0x1012 {01 86 12 10 C3 00}
0x9B0BA 		One Of: CFG Lock, VarStoreInfo (VarOffset/VarName): 0x3C, VarStore: 0x3, QuestionId: 0x145, Size: 1, Min: 0x0, Max 0x1, Step: 0x0 {05 91 66 02 67 02 45 01 03 00 3C 00 10 10 00 01 00}
0x9B0CB 			One Of Option: Disabled, Value (8 bit): 0x0 {09 07 04 00 00 00 00}
0x9B0D2 			One Of Option: Enabled, Value (8 bit): 0x1 (default) {09 07 03 00 30 00 01}
0x9B0D9 		End One Of {29 02}
```

其中 `CFG Lock, VarStoreInfo (VarOffset/VarName): 0x3C, VarStore: 0x3`就是我们要找的。

`VarStoreInfo`的值是CFG Lock这个选项的地址，也可以说其在BIOS中的偏移量。后面的`VarStore`是BIOS默认值，此处为`0x3`。而我们想要解锁它就需要把它修改成`0x0`，即下面对应的Disabled Option，就是需要把`0x3C`这个偏移地址上的数值修改为`0x0`。

**请注意，可变偏移量不仅对于每个主板是唯一的，甚至对于每个固件版本也是唯一的。所以读者应该自己寻找偏移量，切勿在不检查的情况下尝试使用本文中的偏移量。**

#### 修改BIOS偏移量

这里使用的工具是RU.efi。

打开U盘，新建`EFI/BOOT/`目录，将RU.efi重命名为`BOOTX64.efi`放到该目录下。

重启系统，选择从U盘启动，按任意键即可进入RU工具。

- 进入后按 <kbd>alt</kbd>+<kbd>=</kbd> 切换进 "ACPI Variable"；
- 用<kbd>PageUp</kbd>/<kbd>PageDown</kbd>/上下方向键找到"CPUSetup"；
- 回车进入，然后用上下左右方向键找到对应的地址（本文中是`0x3C`，那么就是纵坐标`0030`，横坐标`0C`的位置）；
- 回车，输入`0`就可以看到它变成了0，再按回车确认；
- 按<kbd>Ctrl</kbd> +<kbd>W</kbd>来保存，保存的时候屏幕上会直接显示Updated OK，这说明写入成功；
- 按<kbd>Alt</kbd>+<kbd>Q</kbd> 来退出，然后即可回到引导进入系统了，此时CFG已经解锁。

#### 检查解锁情况

通过 ControlMsrE2 检查，将OpenCore自带的`Tools/ControlMsrE2.efi`加入到Config.plist配置文件中 。接下来，启动 OpenCore 并选择ControlMsrE2.efi条目。这应该会显示以下内容：

```assembly
This firmware has UNLOCKED MSR 0xE2 register!
```

说明MSR 0xE2解锁成功。

#### 修改OpenCore配置文件

在Kernel Option中取消`AppleCpuPmCfgLock`和`AppleXcpmCfgLock`的勾选。

恭喜您完成MSR CFG Lock解锁全过程！现在您将拥有正确的 CPU 电源管理。

#### 参考资料

- [解锁CFG Lock | 张登友的博客 (zdynb.cn)](https://www.zdynb.cn/2020/jie-suo-cfg-lock.html)
- [修复CFG锁-远景论坛-微软极客社区 (pcbeta.com)](https://bbs.pcbeta.com/viewthread-1899638-1-1.html)
- [从Clover到OC （重难点：Insyde BIOS 解锁CFG Lock） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/121655468)