Fiddler script 获取http响应和post的请求body数据

## 前言
系统: Windows
Fiddler版本: Fiddler 5

## fiddler script

> 简单讲，fiddler script提供了请求/响应过程中的事件接口，通过这些接口，我们可以编写代码，注册自定义逻辑。
如：`OnBeforeRequest`、`OnBeforeResponse`定义了请求发送前及响应前的事件接口，在此函数中添加的逻辑，可以在每个http请求前和响应前执行，十分灵活。

## 进入编辑页面

> fiddler script基于JScript.NET编写，如果不熟悉API可能会在使用时造成困惑，为此 fiddler 是带有fiddler script的编辑器：[FiddlerScript Editor](https://www.telerik.com/download/fiddler/fiddlerscript-editor)的
此编辑器提供了语法高亮、智能感知等功能，帮助开发者编写代码。
下载安装后，FiddlerScript Editor可以通过独立窗口打开，

方式1: 直接点击右边的`FiddlerScript`选项卡

![uploads/200212/20200212112857.png](https://s2.ax1x.com/2020/02/12/1HMvA1.png)

方式2: 通过菜单 `规则` -> `自定义规则` (英文: `Rules` -> `Customize Rules`) 来打开 `Fiddler ScriptEditor`

![uploads/200212/20200212113242.png](https://s2.ax1x.com/2020/02/12/1HMLnJ.png)

方式3: 使用自己喜欢的编辑器进行编辑 `CustomRules.js` 文件

win10 存放路径是: `C:\Users\用户名\Documents\Fiddler2\Scripts`(`用户名`要替换为用户名)

![uploads/200212/20200212114125.png](https://s2.ax1x.com/2020/02/12/1HMbX4.png)

## 自定义规则文件中方法介绍和使用演示

#### 1. 自定义规则文件中方法介绍

| 方法名 | 说明 |
| ------ | ------ |
| OnBoot (默认是注释的)| fiddler 启动时调用 |
| OnBeforeShutdown (默认是注释的)| fiddler关闭前调用 |
| OnShutdown (默认是注释的)| fiddler关闭时调用 |
| OnAttach (默认是注释的)| fiddler注册成系统代理时调用 |
| OnDetach (默认是注释的)| fiddler 取消注册系统代理时调用 |
| Main | 在每次fiddler启动时和编译CustomRules.js 脚本时调用。 |
| OnExecAction | 在fiddler窗口的快速处理或者ExecAction.exe 命令行工具时调用 |
| OnBeforeResponse | http请求的响应之前，可在这个方法中修改Response的内容（对应爬虫来说该方法最常用） |
| OnBeforeRequest | 发送http请求之前，可在这个方法中修改或获取Request的内容 |

#### 2. 使用演示

演示的内容有:

1. 给请求的url 添加底色
2. 获取POST请求body的请求参数
3. 将响应结果写入到本地和发送到一个接收数据的地方

```
static function OnBeforeRequest(oSession: Session) {
    // Sample Rule: Color ASPX requests in RED
    // if (oSession.uriContains(".aspx")) {	oSession["ui-color"] = "red";	}

    // Sample Rule: Flag POSTs to fiddler2.com in italics
    // if (oSession.HostnameIs("www.fiddler2.com") && oSession.HTTPMethodIs("POST")) {	oSession["ui-italic"] = "yup";	}

    // Sample Rule: Break requests for URLs containing "/sandbox/"
    // if (oSession.uriContains("/sandbox/")) {
    //     oSession.oFlags["x-breakrequest"] = "yup";	// Existence of the x-breakrequest flag creates a breakpoint; the "yup" value is unimportant.
    // }

    // ... 省略 OnBeforeRequest 方法内默认的代码块

    // 方法内追加的演示代码
	var ci_host = "http://localhost/test/index.php";
    //判断链接的地址包含的链接内容部分，如果匹配到，就进入if 代码块内
    if (oSession.uriContains("/rest/products")) {
        //标记匹配到的uri显示的颜色
        oSession["ui-color"] = "maroon";

        //消除保存的请求可能存在乱码的情况
        oSession.utilDecodeRequest();
        //获取post请求的请求内容块(request body)
        var requestString = oSession.GetRequestBodyAsString();

        //因为post请求头的 Content-type : application/x-www-form-urlencoded
        //所以 请求内容块中的格式是: app_id=100&app_key=200&product_id=1

        //待获取的请求参数名称
        var variable1 = 'product_id';
        //声明存放产品id参数值的变量
        var product_id = "";
        //根据 & 分割请求内容的参数
        var vars = requestString.split('&');
        for (var i = 0; i < vars.length; i++) {
            //根据 = 分割单个键值对,
            var pair = vars[i].split('=');
            //匹配参数名称(键)
            if (decodeURIComponent(pair[0]) == variable1) {
                //如匹配到，将匹配到的参数值赋值到预先定义的变量:product_id
                product_id =  decodeURIComponent(pair[1]);
            }
        }
        
        if(product_id != ""){
            //写日志，可在 fiddler软件右边的窗口的 "日志"选项卡中看到日志输出
            FiddlerObject.log('product_id:' + product_id);

            //请求本地的一个连接来记录监听到的产品id,做相应的处理
            var _xhr2 = new ActiveXObject("Microsoft.XMLHTTP");
            var singleshare_url = ci_host + '/welcome/products/' + encodeURIComponent(product_id);
            _xhr2.onreadystatechange = function() {};
            _xhr2.open("POST", singleshare_url, false);
            _xhr2.setRequestHeader("Content-Type", "application/x-www-form-urlencoded;charset=utf-8");
            _xhr2.send(null);

        }
    }

}


static function OnBeforeResponse(oSession: Session) {
    // ... 省略 OnBeforeResponse 方法内默认的代码块

    var ci_host = "http://localhost/test/index.php";

    //监听一个获取购物车资料的api
    if (oSession.uriContains("/rest/carts")) {
        //消除保存的请求可能存在乱码的情况
        oSession.utilDecodeResponse();
        var jsonString = oSession.GetResponseBodyAsString();
        var myjsonString = jsonString; //传送的json数据
        var responseJSON = Fiddler.WebFormats.JSON.JsonDecode(jsonString);
        if((responseJSON.JSONObject=='System.Collections.ArrayList' || responseJSON.JSONObject=='System.Collections.Hashtable')&&jsonString!='[]'&&jsonString!='{}'){\

            //写入本地一个文件开始
            //构造自己的JSON http请求的信息及返回的结果,
            var str='{}';
            var data = Fiddler.WebFormats.JSON.JsonDecode(str);
            data.JSONObject["request_method"] = oSession.RequestMethod;
            var requestString = oSession.GetRequestBodyAsString();

            data.JSONObject["request_body"]= requestString;
            data.JSONObject["response_data"] = responseJSON.JSONObject;
            data.JSONObject["url"] = oSession.fullUrl;
            data.JSONObject["response_code"] = oSession.responseCode;
            jsonString = Fiddler.WebFormats.JSON.JsonEncode(responseJSON.JSONObject);
            
            // 保存文件到本地
            var fso;
            var file;
            fso = new ActiveXObject("Scripting.FileSystemObject");
            file = fso.OpenTextFile("E:\\fiddler_debug\\test.txt",8 ,true, true);
            file.writeLine(jsonString);
            file.writeLine("\n");
            file.close();
            //写入本地一个文件结束

            //把响应的json内容通过http发送其它地方
            var _xhr = new ActiveXObject('Microsoft.XMLHTTP');
            var url = ci_host + '/welcome/carts';
            //不需要返回值所以写啦个空回调
            _xhr.onreadystatechange = function() {}
            _xhr.open('POST', url, true);
             _xhr.setRequestHeader("Content-Type", "application/json; charset=utf-8");
            //_xhr.setRequestHeader( "Content-Type", "application/x-www-form-urlencoded;charset=utf-8");
            _xhr.setRequestHeader("X-Requested-With", "XMLHttpRequest");
            _xhr.send(myjsonString);
        }
    }

}

```



说明:  php端接收请求头为 `application/json`的 post请求，可用 `$data = file_get_contents("php://input");` 的方式进行接收，如:

```
$data = file_get_contents("php://input");
if($data != null){
   $json_array =  json_decode($data,true);
   var_dump($json_array);
}
```



## 后记

fiddler scriptEditor 右侧面板可查询API接口含义及使用方法编写时有点帮助；

通过 fiddler 官网比较难找到关于 fiddler script 方面的Api接口部分。

JScript.NET 我不熟悉，没有系统学过；

当 边编写 fiddler script时，边用fiddler 进行监听请求时，在保存编写的文件时，会发出有保存成功或失败的提示音；

如果有语法不正确的时候，在fiddler中会弹出错误的提示框和发出错误的提示声音，应及时参看错误的提示内容和关闭错误的提示框；如果已经弹出并未关闭错误的提示框，我试到的情况是保存编写的 fiddler script时，无论语法是否正确，不会再发出保存成功的提示音了。



## References

1. [Fiddler高级用法—Fiddler Script抓取app网页json数据并保存](https://www.cnblogs.com/i-love-python/p/11505669.html)
2. [Fiddler抓包【6】_Fiddler Script](https://www.cnblogs.com/sjl179947253/p/7629048.html)
3. [[fiddler] 使用fiddler script自定义代理规则](https://www.cnblogs.com/hhhyaaon/p/5839283.html)