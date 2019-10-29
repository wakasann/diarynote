使用SOAPui 笔记 


对接Oracle Fusion Application的SOAP. 遇到

```
<env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/">
   <env:Header/>
   <env:Body>
      <env:Fault xmlns:ns0="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd">
         <faultcode>ns0:InvalidSecurity</faultcode>
         <faultstring>InvalidSecurity : error in processing the WS-Security security header</faultstring>
         <faultactor/>
      </env:Fault>
   </env:Body>
</env:Envelope>
```

通过 yandx 搜索 `soapui apply the wss header in the web service header`,通过参考1解决了问题。


> Information about the web services provided by Fusion Applications is hosted in [Oracle Enterprise Repository (OER)](https://www.oracle.com/webfolder/technetwork/docs/HTML/oer-redirect.html). The information provided by OER can be used to understand the functionality provided by the service and how the service can be called.



我个人发布在RTD的文档: [SOAPUI GUIDE](https://integrating-soapui-fusion.readthedocs.io/index.html)




##### References

1. [Integrating With Fusion Application Using Services (SoapUi - SSL)](https://blogs.oracle.com/fadevrel/integrating-with-fusion-application-using-services-soapui-ssl) 主要是通过这个试成功啦
2. [Applying WS-Security to Your Tests](https://www.soapui.org/soapui-projects/ws-security.html) 
3. [Ubuntu 16.04切换/home中文目录为英文目录](https://www.cnblogs.com/EasonJim/p/7123394.html)