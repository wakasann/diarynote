

## Arduino Nokia 5110 LCD屏幕使用小记

硬件:

* Andrino Nuo 
* Nokia 5110 LCD屏幕
* 面包版

Arduino with NOKIA 5110 LCD 

以下的Arduino 代码包含 2个库来自 [Adafruit Industries](https://www.adafruit.com/)
第1个库是驱动 Nokia 5110 LCD(PCD8544 控制器)，可以通过 Arduino IDE 库管理器(项目 -> 加载库 -> 管理器...,在搜索框中输入 `nokia` 进行搜索，然后安装是Adafruit发布的那个)

第2个库是Adafruit graphics library 也可以通过 Arduino IDE的库管理进行安装。

以上的2个库当然可以通过手动安装，下载链接如下:
* [Adafruit Nokia 5110 LCD library](https://github.com/adafruit/Adafruit-PCD8544-Nokia-5110-LCD-library)  ----> [直链](https://github.com/adafruit/Adafruit-PCD8544-Nokia-5110-LCD-library/archive/master.zip)
* [Adafruit graphics library](https://github.com/adafruit/Adafruit-GFX-Library)  ---- > [直链](https://github.com/adafruit/Adafruit-GFX-Library/archive/master.zip)

在下载完成之后，通过 Arduino IDE -> 项目 -> 加载库 -> 添加 .ZIP库 ... 并 选择 .zip 文件(通过上面链接下载的zip 文件)

同样的操作适合其它库文件。

总共引入3个库，引入代码如下:

```
#include <SPI.h>               // 引入 SPI 库
#include <Adafruit_GFX.h>      // 引入 adafruit graphics 库
#include <Adafruit_PCD8544.h>  // 引入 adafruit PCD8544 (Nokia 5110) 库
```



Nokia 5110 LCD 连线到Arduino如下面代码所示:

```
// Nokia 5110 LCD 模块的连线 (CLK, DIN, DC, CE, RST)
Adafruit_PCD8544 display = Adafruit_PCD8544(8, 10, 11, 12, 13);
```



## References

1. [Arduino智能小车超声波测距避障基本思路总结](https://blog.csdn.net/qq_42158309/article/details/80698211)
2. [Arduino with NOKIA 5110 LCD and BMP280 sensor](https://simple-circuit.com/arduino-bmp280-nokia-5110-lcd/) NOKia 5110
3. [[Arduino] Nokia 5110 液晶屏](https://www.jianshu.com/p/8979f3c13c30)