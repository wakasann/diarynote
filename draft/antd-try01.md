react 编写 基于ant.design 页面的参考笔记

## 前言

因为我没有系统的学习 react，是边写边通过搜索引擎找相对的问题，看 [ant.design](https://ant.design/)的 中文文档 编写的一个单页面, 以下的笔记都是写 [gksvideourlr](https://github.com/wakasann/gksvideourlr) 时记录的。



## 重新设定表单输入框的值

```
//this.props.form.resetFields(); //重置表单的值 initvalues
//直接设定值
 this.props.form.setFieldsValue({
              username: "",
            });
this.props.form.validateFields(); //再次调用验证表单方法，对表单再次验证
```

## react中setState后，没有立即获取到最新的state中的值

在 `setState` 方法添加匿名函数回调


```
this.setState({
      showVideo: checked,
    },() =>{
      console.log(this.state.showVideo);
    });
```
## React使用build 非 root

在项目的`package.json`文件中增加一个节点`“homepage”:"."`，或者是`"./"`


```
{
  "name": "ksvdv",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "@testing-library/jest-dom": "^4.2.4",
    "@testing-library/react": "^9.3.2",
    "@testing-library/user-event": "^7.1.2",
    "antd": "^3.26.2",
    "axios": "^0.19.0",
    "js-cookie": "^2.2.1",
    "react": "^16.12.0",
    "react-dom": "^16.12.0",
    "react-scripts": "3.3.0"
  },
  "homepage": ".",
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  }
}
```
## react 使用 cookie

地址: [https://github.com/js-cookie/js-cookie](https://github.com/js-cookie/js-cookie)

安装 `js-cookie`

```
npm i js-cookie --save
```

在项目中引入，如在 `App.js`内容开头的 `import`语句后面添加

```
import Cookies from 'js-cookie';
```

使用

```
//获取cookie的值
let show_video = Cookies.get('show_video');
//设置一个cookie
Cookies.set('key','value');
```

如果将 boolean 类型的数据存入到 cookie中，取出将是字符型的 boolean值，不能直接与 boolean类型的值进行判断值是否相等，可以考虑存放到cookie的boolean类型数据使用1和0进行代替。

这是我存放 获取存入boolean类型的 cookie的处理。

```
let show_video = Cookies.get('show_video');
if(show_video === undefined){
  show_video = true;
}else{
  show_video = (show_video === "false")?false:true;
}
Cookies.set('show_video',show_video);
```

## React axios POST 请求

安装 axios

```
npm install axios --save
```

引入

```
import axios from 'axios';
//axios的qs库
import Qs from 'qs';
```

发送 `Content-type` 为 `application/x-www-form-urlencode` POST 请求

```
axios.post('request_url',Qs.stringify({
          username: 'username',
          password: 'password'
      })
      ).then(res=>{
        console.log("res",res)           
      })
```

```
//如果不使用 axios的 qs 库，也可以使用URLSearchParams接口
let param = new URLSearchParams();
param.append("username", "username");
param.append("password", "password");
```


## react 显示html

显示我的 state的 `videoHtml`

```
this.state={
      videoHtml: "<div>Hello world</div>"
    }
```


```
<div className="video-wrapper" dangerouslySetInnerHTML={{ __html: this.state.videoHtml }}></div>
```

固定显示html

```
<div className="video-wrapper" dangerouslySetInnerHTML={{ __html: '<div>Hi</div>' }}></div>
```


> 原理：
> 1.dangerouslySetInnerHTMl 是React标签的一个属性，类似于angular的ng-bind；
> 2.有2个{{}}，第一{}代表jsx语法开始，第二个是代表dangerouslySetInnerHTML接收的是一个对象键值对;
> 3.既可以插入DOM，又可以插入字符串；
> 4.不合时宜的使用 innerHTML 可能会导致 cross-site scripting (XSS) 攻击。 净化用户的输入来显示的时候，经常会出现错误，不合适的净化也是导致网页攻击的原因之一。dangerouslySetInnerHTML 这个 prop 的命名是故意这么设计的，以此来警告，它的 prop 值（ 一个对象而不是字符串 ）应该被用来表明净化后的数据。



## References

### Antd Form 清空表单
* [Antd form表单的使用、设值、取值、清空值](https://blog.csdn.net/qq_36658051/article/details/89924837)
 * [react antd 清空form表单](https://www.jianshu.com/p/f332116c9382)
 * [React 使用antd 清空表单](https://www.cnblogs.com/yinhao-jack/p/10570072.html)
 * [React Antd 关于Input表单清空的坑](https://www.jianshu.com/p/dc75bf2870e2)
 * [Form表单](https://ant.design/components/form-cn/)
### react setState
* [关于react中setState后，没有立即获取到最新的state中的值](https://www.jianshu.com/p/65587370bd91)

### react build
 * [React使用build生成项目：资源文件路径"/"修改为相对路径"."](https://blog.csdn.net/mistyrain0109/article/details/94914320)

### react html

[react中dangerouslySetInnerHTML使用（简洁）](https://blog.csdn.net/exploringfly/article/details/80582859)

## react post 

* [vue中使用axios最详细教程](https://www.cnblogs.com/nogodie/p/9853660.html)
* [URLSearchParams](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams)
* [axios的简单使用](https://www.cnblogs.com/evaling/p/7607603.html)