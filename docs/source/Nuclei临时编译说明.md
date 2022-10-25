# Nuclei临时编译说明

## 环境准备

- 安装Nuclei SDK，内网路径：[\\192.168.2.2\injoinic\Project\IP\RISC-V\芯来科技\工具链\NucleiStudio_IDE_202204-win64.zip]()

- 获取示例工程，见附件：test.zip



## 导入工程

解压缩SDK后，可以在目录下看到`Nuclei_Studio_User_Guide.pdf`，这个是IDE的使用说明，建议先看看。而后运行`NucleiStudio.exe`。

![image-20220819162010006](https://markdown-1306347444.cos.ap-shanghai.myqcloud.com/img/image-20220819162010006.png)







解压缩完文件后，打开IDE环境，选择：`File->Open Projects from File System...`。

![image-20220819161220132](https://markdown-1306347444.cos.ap-shanghai.myqcloud.com/img/image-20220819161220132.png)

而后，选择`Directory...`，选中解压缩后的工程文件，`Finish`即可。

![image-20220819161357278](https://markdown-1306347444.cos.ap-shanghai.myqcloud.com/img/image-20220819161357278.png)



工程配置这里，需要选择`test_debug_openocd`，目前只支持**openocd**下载方式。

![image-20220819161730429](https://markdown-1306347444.cos.ap-shanghai.myqcloud.com/img/image-20220819161730429.png)



正确执行后，会停在`main`函数入口。

![image-20220819161845615](https://markdown-1306347444.cos.ap-shanghai.myqcloud.com/img/image-20220819161845615.png)



编译完成后的东西在`Debug`目录下，自己根据需要使用。

![image-20220819162222984](https://markdown-1306347444.cos.ap-shanghai.myqcloud.com/img/image-20220819162222984.png)





## 常见问题

### 导入工程失败

由于项目中已经有一个同名工程，所以无法导入。

![image-20220819162324050](https://markdown-1306347444.cos.ap-shanghai.myqcloud.com/img/image-20220819162324050.png)

简单的办法是`Delete`之前的工程，导入这个新工程。当然可以用下面的修改工程名称方法。

![image-20220819162426118](https://markdown-1306347444.cos.ap-shanghai.myqcloud.com/img/image-20220819162426118.png)





### 修改工程名称

打开示例工程，修改`.project`中的红框位置。

![image-20220819162635056](https://markdown-1306347444.cos.ap-shanghai.myqcloud.com/img/image-20220819162635056.png)



修改launch文件名，并修改里面的一些关于`test`的配置，不然进入工程后，左上角的框框不会正常显示。

![image-20220819164157375](https://markdown-1306347444.cos.ap-shanghai.myqcloud.com/img/image-20220819164157375.png)

![image-20220819163303412](https://markdown-1306347444.cos.ap-shanghai.myqcloud.com/img/image-20220819163303412.png)



鼠标放在工程名处,右键->Properties->C/C++ Build->Settings->Build Steps->Post-build steps。修改工程配置，调整Build Steps的结果，这里是控制生成`.bin .verilog .dump`的地方 ，修改将`test`修改成你替换后的名称，也就是`test_123`。最终配置如下：

`riscv-nuclei-elf-objcopy -O verilog test_123.elf test_123.verilog;riscv-nuclei-elf-objdump -D -s test_123.elf >test_123.dump;riscv-nuclei-elf-objcopy -O binary test_123.elf test_123.bin`

![image-20220819163026653](https://markdown-1306347444.cos.ap-shanghai.myqcloud.com/img/image-20220819163026653.png)





### 修改了东西工程没编译

修改.ld或者.s后，IDE并不会识别到改动，再次编译并不会真的编译工程。可以用两个方式来让IDE重新编译：

- 删除Debug/Release目录，简单直接。
- 修改任意一个.c文件，加一个换行或者空格就行。







### 无法烧录或者调试



#### 情况1：libusb驱动正确加载，但是无法连接；

解决办法：重新上电FPGA板即可。

```
Error: JTAG scan chain interrogation failed: all zeroes
Error: Check JTAG interface, timings, target power, etc.
Error: Trying to use configured scan chain anyway...
Error: riscv.cpu: IR capture error; saw 0x00 not 0x01
```

![image-20220909104940221](https://markdown-1306347444.cos.ap-shanghai.myqcloud.com/img/image-20220909104940221.png)







正确的信息应该类似如下显示。

![image-20220909104657110](https://markdown-1306347444.cos.ap-shanghai.myqcloud.com/img/image-20220909104657110.png)











#### 情况2：JTAG没有正常连接

解决办法：连接JTAG到电脑，并确保驱动安装正常。

如果JTAG连接了，但还是提示`Info : no device found, trying D2xx driver`，那就按照情况3处理。

```
Info : no device found, trying D2xx driver
Info : D2xx device count: 0
Warn : D2xx driver found nothing, falling back to libusb...
Error: unrecognized USB device descriptor
Error: unable to open ftdi device with vid 0403, pid 6010, description '*', serial '*' at bus location '*'
```

![image-20220909110249213](https://markdown-1306347444.cos.ap-shanghai.myqcloud.com/img/image-20220909110249213.png)







#### 情况3：驱动没正确安装（冲突），提示：Info : no device found, trying D2xx driver

解决办法：重新安装驱动。

请确保连接上后，驱动显示如下信息：

如果没有，可能和电脑上的一些驱动冲突了（Python HCI测试平台使用后容易出现）。尝试到Nuclei重新下载驱动[RISC-V开发板_专业RISC-V处理器IP及解决方案公司 (nucleisys.com)](https://www.nucleisys.com/developboard.php#debuggerkit)，再安装一次驱动就行，知道设备管理器有如下信息即可。

![image-20220909104419557](https://markdown-1306347444.cos.ap-shanghai.myqcloud.com/img/image-20220909104419557.png)

![image-20220909104537953](https://markdown-1306347444.cos.ap-shanghai.myqcloud.com/img/image-20220909104537953.png)







#### 情况4：硬件连接不正确

解决办法：确保接线正确

遇到提示如下，确实在使用libusb driver，但是TAP无法连接，考虑硬件接线异常。

```
Info : Using libusb driver
Info : clock speed 1000 kHz
Info : hbird_debugger: This TAP's version is too old, trying use async sequence to handshake..
Info : hbird_debugger: This TAP's version is too old, trying use async sequence to handshake..
Info : TAP riscv.cpu does not have valid IDCODE (idcode=0x0)
Info : TAP auto0.tap does not have valid IDCODE (idcode=0x0)
Info : TAP auto1.tap does not have valid IDCODE (idcode=0x0)
Info : TAP auto2.tap does not have valid IDCODE (idcode=0x0)
Info : TAP auto3.tap does not have valid IDCODE (idcode=0x0)
Info : TAP auto4.tap does not have valid IDCODE (idcode=0x0)
Info : TAP auto5.tap does not have valid IDCODE (idcode=0x0)
Info : TAP auto6.tap does not have valid IDCODE (idcode=0x0)
Info : TAP auto7.tap does not have valid IDCODE (idcode=0x0)
Info : TAP auto8.tap does not have valid IDCODE (idcode=0x0)
Info : TAP auto9.tap does not have valid IDCODE (idcode=0x0)
Info : TAP auto10.tap does not have valid IDCODE (idcode=0x0)
Info : TAP auto11.tap does not have valid IDCODE (idcode=0x0)
Info : TAP auto12.tap does not have valid IDCODE (idcode=0x0)
Info : TAP auto13.tap does not have valid IDCODE (idcode=0x0)
Info : TAP auto14.tap does not have valid IDCODE (idcode=0x0)
Info : TAP auto15.tap does not have valid IDCODE (idcode=0x0)
Info : TAP auto16.tap does not have valid IDCODE (idcode=0x0)
Info : TAP auto17.tap does not have valid IDCODE (idcode=0x0)
Info : TAP auto18.tap does not have valid IDCODE (idcode=0x0)
Info : TAP auto19.tap does not have valid IDCODE (idcode=0x0)
Warn : Unexpected idcode after end of chain: 21 0x00000000
Warn : Unexpected idcode after end of chain: 53 0x00000000
Warn : Unexpected idcode after end of chain: 85 0x00000000
Warn : Unexpected idcode after end of chain: 117 0x00000000
Warn : Unexpected idcode after end of chain: 149 0x00000000
Warn : Unexpected idcode after end of chain: 181 0x00000000
Warn : Unexpected idcode after end of chain: 213 0x00000000
Warn : Unexpected idcode after end of chain: 245 0x00000000
Warn : Unexpected idcode after end of chain: 277 0x00000000
Warn : Unexpected idcode after end of chain: 309 0x00000000
Warn : Unexpected idcode after end of chain: 341 0x00000000
Warn : Unexpected idcode after end of chain: 373 0x00000000
Warn : Unexpected idcode after end of chain: 405 0x00000000
Warn : Unexpected idcode after end of chain: 437 0x00000000
Warn : Unexpected idcode after end of chain: 469 0x00000000
Warn : Unexpected idcode after end of chain: 501 0x00000000
Warn : Unexpected idcode after end of chain: 533 0x00000000
Warn : Unexpected idcode after end of chain: 565 0x00000000
Warn : Unexpected idcode after end of chain: 597 0x00000000
Warn : Unexpected idcode after end of chain: 629 0x00000000
Error: double-check your JTAG setup (interface, speed, ...)
Error: Trying to use configured scan chain anyway...
Error: riscv.cpu: IR capture error; saw 0x00 not 0x01
Info : hbird_debugger: This TAP's version is too old, trying use async sequence to handshake..
Warn : Bypassing JTAG setup events due to errors
Error: dtmcontrol is 0. Check JTAG connectivity/board power.
Warn : target riscv.cpu examination failed
Info : starting gdb server for riscv.cpu on 3333
Info : Listening on port 3333 for gdb connections
Error: Target not examined yet
```

![image-20220909111042655](https://markdown-1306347444.cos.ap-shanghai.myqcloud.com/img/image-20220909111042655.png)





确保接线如下图所示。JTAG接口使用的是cJTAG模式，目前只使用3根线，分贝是：TCK，TMS和GND。后续需要连接串口终端再考虑连接UartTx和UartRx。

![image-20220909111322910](https://markdown-1306347444.cos.ap-shanghai.myqcloud.com/img/image-20220909111401122.png)



![image-20220909111350111](https://markdown-1306347444.cos.ap-shanghai.myqcloud.com/img/image-20220909111350111.png)





![image-20220909111436729](https://markdown-1306347444.cos.ap-shanghai.myqcloud.com/img/image-20220909111436729.png)
