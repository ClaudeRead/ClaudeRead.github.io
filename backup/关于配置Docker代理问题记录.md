1、对于Windows下桌面版本的Docker，如果本机开放了代理端口，比如本机开放了7892这个端口，现在需要让本地docker的流量也能走7892，配置如下：
    - 在Docker桌面版中选择设置，找到Proxies选项，打开Manual proxy configuration，分别对HTTP和HTTPS对话框中填入http://127.0.0.1:7892，最后点击Apply
    - 更改config文件，一般在"%USERPROFILE%\.docker\config.json"，或者使用命令：`notepad "%USERPROFILE%\.docker\config.json"`打开并修改配置文件，(如果是Clash系列软件)在配置文件中添加下列代码：
        ```
            "proxies": {
		        "default": {
			        "httpProxy": "http://host.docker.internal:7892",
			        "httpsProxy": "http://host.docker.internal:7892",
			        "noProxy": "localhost,127.0.0.1,::1,host.docker.internal"
		        }
	        }
        ```
    - 如果 7892 是 SOCKS5 端口，应根据代理软件的实际协议设置 allProxy；则使用下来配置代码，同时确认 httpProxy 和 httpsProxy 指向的协议与端口兼容。
        ```
            "proxies": {
		        "default": {
			        "httpProxy": "http://host.docker.internal:7892",
			        "httpsProxy": "http://host.docker.internal:7892",
                                "allProxy": "socks5://host.docker.internal:7892",
			        "noProxy": "localhost,127.0.0.1,::1,host.docker.internal"
		        }
	        }
        ```
    - 保存后重启docker，然后再命令行中测试`docker run --rm alpine sh -c 'env | grep -i proxy'`如果配置正确会有：
        ```
            HTTP_PROXY=http://host.docker.internal:7892
            HTTPS_PROXY=http://host.docker.internal:7892
        ```
    - 确认容器真的能联网,在命令行中再输入：`docker run --rm curlimages/curl:latest -x http://host.docker.internal:7892 -I https://www.google.com`，出现如下数据包，则说明配置生效
       
        ```
            C:\Users\ReadPond>docker run --rm curlimages/curl:latest -x http://host.docker.internal:7892 -I https://www.google.com
            Unable to find image 'curlimages/curl:latest' locally
            latest: Pulling from curlimages/curl
            3f72b3b77eb8: Pull complete
            Digest: sha256:马赛克
            Status: Downloaded newer image for curlimages/curl:latest
            % Total    % Received % Xferd  Average Speed  Time    Time    Time   Current
                                            Dload  Upload  Total   Spent   Left   Speed
            0      0   0      0   0      0      0      0                              0HTTP/1.1 200 Connection established
            0      0   0      0   0      0      0      0                              0
            HTTP/2 200
            content-type: text/html; charset=ISO-8859-1
            content-security-policy-report-only: object-src 'none';base-uri 'self';script-src 'nonce-马赛克' 'strict-dynamic' 'report-sample' 'unsafe-eval'   'unsafe-inline' https: http:;report-uri https://csp.withgoogle.com/csp/gws/other-hp
            accept-ch: Sec-CH-Prefers-Color-Scheme
            p3p: CP="This is not a P3P policy! See g.co/p3phelp for more info."
            date: Tue, 27 Oct 2026 09:22:58 GMT
            server: gws
            x-xss-protection: 0
            x-frame-options: SAMEORIGIN
            expires: Tue, 27 Oct 2026 09:22:58 GMT
            cache-control: private
            set-cookie: __Secure-STRP=马赛克; expires=Tue, 27-Oct-2026 09:27:58 GMT; path=/; domain=.google.com; Secure; SameSite=strict
            set-cookie: AEC=马赛克; expires=Sun, 25-Apr-2027 09:22:58 GMT; path=/; domain=.google.com; Secure; HttpOnly; SameSite=lax
            set-cookie: 马赛克; expires=Wed, 28-Apr-2027 09:22:58 GMT; path=/; domain=.google.com; HttpOnly
            alt-svc: h3=":443"; ma=2592000,h3-29=":443"; ma=2592000

            0      0   0      0   0      0      0      0                              0

        
        ```
    

    - 注意：如果出现`WARNING: Error parsing config file (C:\Users\ReadPond\.docker\config.json): invalid character '"' after object key:value pair Unable to find image 'alpine:latest' locally`,请检查配置文件是否写正确，配置文件格式是json格式！！！
    
        