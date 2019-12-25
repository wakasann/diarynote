树莓派使用3.5mm 耳机口输出音频

1. 设置 音频输出

通过以下命令设置

```
#From raspberry pi ssh
sudo raspi-config
```

选择`Advanced Options`回车

选择`Audio`回车

有三个选项`Auto`， `HDMI`， `3.5mm`

需要3.5mm输出的话选择对应3.5的选项

2. 解决选择`3.5mm`音频选项时出现`There was an error running option A8 Audio `

如果选择 `3.5mm`端口后出现类似

```
There was an error running option A8 Audio  
```

的提示，需要编辑`/boot/config.txt`文件，在该文件内容末尾添加`dtparam=audio=on`；

```
#From raspberry pi ssh
sudo reboot # 重启树莓派
```

下一步重启树莓派，使修改的内容设置生效.



重启完成之后，重复第1点的操作,设置成功之后的输出类似如下:

```
pi@raspberrypi:~ $ sudo raspi-config 
numid=3,iface=MIXER,name='PCM Playback Route'
  ; type=INTEGER,access=rw------,values=1,min=0,max=2,step=0
  : values=1
```



3. 播放mp3

安装 mplayer2 播放器

```
sudo apt-get install mplayer2
```



可以通过安装alsa工具来调整音量

命令如下

```
sudo apt-get install alsa-utils alsa-tools
```


在终端输入`alsamixer`命令后就可以开始调整输出音频的音量



## References

* [树莓派声音设置 there was an error running option a4 audio - csdn](https://blog.csdn.net/fronde_wu/article/details/84750091)
* [树莓派 使用3.5耳机口输出音频](https://www.cnblogs.com/mlh-bky/p/9571914.html)
* [树莓派3 播放音乐(网络音乐)](https://www.jianshu.com/p/4082f8998412)