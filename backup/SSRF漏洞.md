### SSRF

&emsp;&emsp;Server-Slde Request Forgery：服务器端请求伪造。是一种由攻击者构造特殊形式的请求，并且由指定服务器端发起的恶意请求的一个安全漏洞，由于业务运行的服务器处于内外网边界，并且可通过利用当前这台服务器根据所在的网络，访问到与外部网路隔离的内网应用，所以一般情况下，SSRF漏洞攻击目标是攻击者无法直接访问的内网系统

<img width="934" height="545" alt="Image" src="https://github.com/user-attachments/assets/c216a46b-e932-4130-a81e-64a7f5bcf73e" />

目标：从外网无法访问到内部系统
原因：由于服务器提供了从其他服务器应用获取数据的功能且没有对目标地址做过滤与限制

#### 如何挖掘：

&emsp;1. 分享：通过URL地址分享网页内容
&emsp;2. 转码服务：通过URL地址把源地址的网页内容调优使其适合手机屏幕浏览
&emsp;3. 在线翻译：通过URL地址翻译对应文本
&emsp;4. 图片加载与下载：通过URL地址下载或加载图片
&emsp;5. 图片，文章收藏
&emsp;6. 未空开的api实现以及其他调用URL功能
&emsp;7. URL敏感参数：share、wap、url、link、u、3g、imageURL、domain

#### 漏洞危害：

&emsp;探测内网资产存活
&emsp;攻击内网资产：以内网的身份攻击内网
&emsp;任意文件读取
&emsp;检查IP是否是内网IP
&emsp;如果存在漏洞，但是又找不到内网地址情况下---直接爆破！！！！

#### 漏洞函数：

&emsp;PHP(请求函数):
&emsp;&emsp;file_get_contents()
&emsp;&emsp;fsockopen()
&emsp;&emsp;fopen()
&emsp;&emsp;curl_exec()
&emsp;&emsp;curl()
&emsp;&emsp;read_file()
&emsp;JAVA（请求类）:
&emsp;&emsp;imagel
&emsp;&emsp;HttpClient
&emsp;&emsp;OKHTTP
&emsp;&emsp;HTTPRequest

#### 如何绕过对方防御机制

&emsp;- 将IP地址转化为其他进制 ---黑名单(http://0/)
&emsp;- @符号绕过---白名单 http://www.baidu.com@http://127.0.0.1
&emsp;- 短网址绕过----www.dwz.lc
&emsp;- 302跳转绕过
&emsp;- DNS重绑定(再第二次发起攻击)
&emsp;- 句号绕过eg:http://127。0。0。1
&emsp;- Enclosed alphanumerics绕过(icon符号)

#### 伪协议

&emsp;- http协议：传统HTTP协议，用于探测端口，可获取文件本身。eg：http://www.baidu.com
&emsp;- file协议：  读取文件协议，可获取文件中的内容。eg：file://C:/window/win.ini
&emsp;- dict协议：  字典服务器协议，用于探测端口。eg：dict://127.0.0.1:6379/info
&emsp;- ftp协议：  FTP文件服务器协议
&emsp;- gopher协议
&emsp;- FastCGI协议

#### 防御漏洞

&emsp;- 限制协议为HTTP/HTTPS
&emsp;- 禁止30X跳转
&emsp;- 设置URL白名单或者限制内网IP(使用gethostbyname()判断是否为内网IP
&emsp;- 服务端开启OpenSSL无法交互利用
&emsp;- 服务端需要认证交互
&emsp;- 把用于取外网资源的API部署在不属于自己的机房
&emsp;- 过滤返回信息,验证远程服务器对请求的响应是比较容易的方法。如果web应用是去获取某一种类型的文件。那么在把返回结果展示给用户之前先验证返回的信息是否符合标准。
&emsp;- 限制请求的端口为http常用的端口，比如 80、443、8080、8090
&emsp;- 统一错误信息，避免用户可以根据错误信息来判断远端服务器的端口状态。

#### 备注：
302跳转绕过：
&emsp;&emsp;- 前提是服务器要允许30x跳转，  如果后端服务器在接收到参数后，正确的解析了URL的host，并且进行了过滤，我们这个时候可以使用302跳转的方式来进行绕过。
&emsp;&emsp;- 302跳转和307跳转的区别,307跳转回转发POST请求中的 数据等,但是302跳转不会

DNS重绑定(再第二次发起攻击)：
&emsp;&emsp;- 使用DNS重绑定的前提是攻击者需要对DNS服务器有绝对控制，在上面运行自编的解析服务，使其每次返回的地址均不同并且攻击者需要有自己的域名，这个域名绑定了两条A记录，最后将TTL = 0，防止响应缓存(一旦缓存，重绑定将无法生效)
&emsp;&emsp;- 重绑定前提是先绑定，触发了之后在重新绑定，绑定的是DNS中的解析，解析是A记录(IP地址)
&emsp;&emsp;- 在该网址中构建请求地址，获得临时域名
&emsp;&emsp;&emsp;&emsp;- 防御检测代码会首先请求一次域名，判断Ip地址是否是白名单
&emsp;&emsp;&emsp;&emsp;- DNS服务器在收到首次请求的时候，返回允许的IP地址
&emsp;&emsp;&emsp;&emsp;- 防御检测代码执行后，服务器真正发起DNS请求，请求资源，此时DNS记录以0毫秒的延迟(TTL=0)，修改IP地址为127.0.0.1达到绕过SSRF防护的作用

<img width="874" height="330" alt="Image" src="https://github.com/user-attachments/assets/cf1acf96-b390-4e07-8013-8de254267206" />
