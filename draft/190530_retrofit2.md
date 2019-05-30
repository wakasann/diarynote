今天晚上了解到 retrofit2 有一个叫MVP模式的封装


安装的组件

1. [rxjava](https://github.com/ReactiveX/RxJava)
2. [retrofit](https://github.com/square/retrofit)
3. [okhttp](https://github.com/square/okhttp)

各组件里面提供其它的组件，原来都在源码的第1层都能找到的

我的引入部分:

```
/** rxjava **/
implementation 'io.reactivex.rxjava2:rxandroid:2.1.1'
implementation 'io.reactivex.rxjava2:rxjava:2.2.9'

/** retrofit **/
implementation 'com.squareup.retrofit2:retrofit:2.5.0'
implementation 'com.squareup.retrofit2:converter-gson:2.5.0'
implementation 'com.squareup.retrofit2:adapter-rxjava2:2.5.0'

/**  okhttp  **/
implementation("com.squareup.okhttp3:okhttp:3.14.2")
implementation("com.squareup.okhttp3:logging-interceptor:3.14.2")
```



##### Reference
1. [小白能看懂的MVP+RXjava+Retrofit2详细讲解](https://www.jianshu.com/p/426864584518)
2. [Rxjava2+Retrofit2+MVP的网络请求封装](https://www.jianshu.com/p/193d8c37c73c)
3. [【Android - 框架】之RxJava的使用](https://www.cnblogs.com/itgungnir/p/6210844.html)
4. [给 Android 开发者的 RxJava 详解](http://gank.io/post/560e15be2dca930e00da1083#toc_1) 自己通过这篇文章来了解rxjava :smile: