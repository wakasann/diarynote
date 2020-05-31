arduino-ds18b20

## 下载类库
1. [OneWire releases](https://github.com/PaulStoffregen/OneWire/releases) -- v2.3.5
2. [Arduino-Temperature-Control-Library releases](https://github.com/milesburton/Arduino-Temperature-Control-Library/releases) --v3.8.0

我的做法是将下载的压缩包解压，然后将文件放入到项目的目录下,我的ardunio 项目里的文件层级是:
```
- 37-temp-18b20.ino
- DallasTemperature.cpp
- DallasTemperature.h
- OneWire.cpp
- OneWire.h
- util
-- OneWire_direct_gpio.h
-- OneWire_direct_regtype.h
```

## 接线

我使用的是 37 in 1 Sensors Kit for Arduino里面的 TEMP 18B20 模块，三个引脚上面标识: `GRY`，

G -> 地线,R -> 电源5V,Y -> 2数据库



## 使用

修改`DallasTemperature.h` 22行的 `#include <OneWire.h>` 为 `#include "OneWire.h"`

```
//#include <Arduino.h>
//#include "OneWire.h"
#include "DallasTemperature.h"
//G -> GND, R -> VCC,Y -> 2
// Data wire is plugged into port 2 on the Arduino
#define ONE_WIRE_BUS 2
// Setup a oneWire instance to communicate with any OneWire devices (not just Maxim/Dallas temperature ICs)
OneWire oneWire(ONE_WIRE_BUS);
// Pass our oneWire reference to Dallas Temperature. 
DallasTemperature sensors(&oneWire);
void setup()
{
  // start serial port
  Serial.begin(9600);
  Serial.println("Dallas Temperature IC Control Library Demo");
  // Start up the library
  sensors.begin();
}
void loop()
{ 
  // call sensors.requestTemperatures() to issue a global temperature 
  // request to all devices on the bus
  Serial.print("开始获取温度...");
  sensors.requestTemperatures(); // Send the command to get temperatures
  Serial.println("获取完成");
  
  Serial.print("设备1(索引 0),温度是: ");
  Serial.println(sensors.getTempCByIndex(0));
  delay(1000);
}
```



----

```
//Steinhart-Hart Thermistor 方程的应用
#include <math.h>
double Thermister(int RawADC) {
 double Temp;
 Temp = log(((10240000/RawADC) - 10000));
 Temp = 1 / (0.001129148 + (0.000234125 + (0.0000000876741 * Temp * Temp ))* Temp );
 Temp = Temp - 273.15; // Convert Kelvin to Celcius
 return Temp;
}
```





## References

1. [在Arduino中使用DS18B20温度传感器（基于OneWire和DallasTemperature库）](https://blog.csdn.net/Naisu_kun/article/details/88420357)