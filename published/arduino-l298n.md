Arduino连接L298n驱动板驱动小车的电机



## 1、L298N介绍

> 先来讲讲电机驱动，驱动一般使用L298N，L298N  是一种双H桥电机驱动芯片，其中每个H桥可以提供2A的电流，功率部分的供电电压范围是2.5-48v，逻辑部分5v供电，接受5vTTL电平。现在有很多成品模块卖，而且价格也不贵，很适合大家diy智能小车。L298N模块多数都是长下边这个样子，这里要注意，L298N模块12v是给模块供电，5v是输出，意思就是L298N的模块接入12V电源，同时输出5V电源可以给其他模块供电。
> 图:
> ![uploads/200214/001.jpg](https://s2.ax1x.com/2020/02/14/1vFwF0.jpg)

## 接线

输出A和输出B: 连接小车电机

12V + : 接电池盒子的正极

GND: 接电池盒子的负极(如连了USB进行调试时，不接方形电池的DC线扣的原型)

5v+: 因为板载5V是使能的，通过杜邦线连接方形电池的DC线扣的方形部分

IN1 ~ IN4: 按顺序接 Anrduin UNO 2,3,4,5 pin 口

![uploads/200214/IMG_20200214_214803.jpg](https://s2.ax1x.com/2020/02/14/1vF0YV.jpg)



> 需要注意的一点是arduino必须和驱动板为同一电压源输入，例如我这里就是12v的**电池**输入，当然除了DC口也可以通过arduino上的vim口输入（在两个接地口下方）
>
> 或者
>
> 如果L298N和arduino使用不同的电源供电的话，那么需要将arduino的GND和模块上的GND连接在一起，只有这样单片机上过来的逻辑信号才有个参考0点。此点非常重要

我的代码是:

```
int in1 = 2;
int in2 = 3;
int in3 = 4;
int in4 = 5;

int row_speed = 100; //轮子的转速


void setup() {
  // put your setup code here, to run once:
  pinMode(in1,OUTPUT);
  pinMode(in2,OUTPUT);
  pinMode(in3,OUTPUT);
  pinMode(in4,OUTPUT);
  
  car_stop();
}

void loop() {
  // put your main code here, to run repeatedly:
  go();
  delay(2000);
  back();
  delay(2000);
  go_left();
  delay(2000);
  go_right();
  delay(2000);
  car_stop();
  delay(2000);
}

void car_stop()
{
  digitalWrite(in1,LOW);
  digitalWrite(in2,LOW);
  digitalWrite(in3,LOW);
  digitalWrite(in4,LOW);
}

void go()
{
  digitalWrite(in1,HIGH);
  analogWrite(in2,row_speed);
  //digitalWrite(in2,LOW);
  digitalWrite(in3,LOW);
  //digitalWrite(in4,HIGH);
  analogWrite(in4,row_speed);
}

void back()
{
  digitalWrite(in1,LOW);
  analogWrite(in2,row_speed);
  digitalWrite(in3,HIGH);
  analogWrite(in4,row_speed);
}

void go_left()
{
  digitalWrite(in1,HIGH);
  analogWrite(in2,row_speed);
  digitalWrite(in3,LOW);
  digitalWrite(in4,LOW);
}

void go_right()
{
  digitalWrite(in1,LOW);
  analogWrite(in2,LOW);
  digitalWrite(in3,HIGH);
  analogWrite(in4,row_speed);
}
```

## References

* [arduino产生pwm波+l298n驱动小车电机的三种方式](http://www.cirmall.com/bbs/thread-161328-1-1.html) 担心车速块，那么3,5 的2个就可以通过PWM波的方式减慢车速
* [Arduino 平衡车 MPU6050 L298N](https://www.geek-workshop.com/thread-36563-1-1.html)
* [Arduino+L298N小车实例教程](https://www.arduino.cn/thread-83331-1-1.html)
* [微型直流电机控制基本方法 L298N模块](https://www.cnblogs.com/MyAutomation/p/9289520.html)

