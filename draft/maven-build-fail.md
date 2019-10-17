Fatal error compiling: invalid target release: 11 -> [Help 1]

```
<plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.0</version>
                <configuration>
                    <source>11</source>
                    <target>11</target>
                </configuration>
            </plugin>
```

```
D:\workspace\java\signal-server>mvn -v
Apache Maven 3.6.2 (40f52333136460af0dc0d7232c0dc0bcf0d9e117; 2019-08-27T23:06:16+08:00)
Maven home: E:\develop\apache-maven\bin\..
Java version: 1.8.0_201, vendor: Oracle Corporation, runtime: C:\Program Files\Java\jdk1.8.0_201\jre
Default locale: zh_CN, platform encoding: GBK
OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"
```

根据参考文字，将 `plugin`中的`source`改为当前 jdk的版本

```
<plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.0</version>
                <configuration>
                    <source>1.8</source>
                    <target>11</target>
                </configuration>
            </plugin>
```



## References
1. [Fatal error compiling: 无效的目标发行版: 1.8 -> [Help 1\] (zhuan)](https://www.cnblogs.com/zhao1949/p/6179968.html)