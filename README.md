# Argo tunnel使用教程
## 第一步：将域名托管到cloudflare，具体操作办法参考：
```
https://jaycechant.info/2020/name-server-from-dnspod-to-cloudflare/
```
## 第二步：从以下网址下载对应cloudflared
```
https://github.com/cloudflare/cloudflared/releases/
```

比如下载amd64版本：
```
wget -O /root/cloudflared https://github.com/cloudflare/cloudflared/releases/download/2021.8.1/cloudflared-linux-amd64
```

将cloudflared移动到usr/bin/目录
```
mv /root/cloudflared /usr/bin/cloudflared
```
## 第三步：开启argo tunnel穿透

输入如下命令授权argo tunnel穿透
```
cloudflared login
```
输入上述命令之后会生成一个授权链接，复制该链接在浏览器中打开（请提前登陆cloudflare账户）
在打开的页面点击域名启用对设备的域名穿透授权

回到linux操作界面输入如下命令：
```
cloudflared tunnel --hostname <tunnelDomain> --url localhost:端口
```
比如要穿透本地的1234端口，域名为xxx.com，A记录为abc，则输入命令格式如下：
```
cloudflared tunnel --hostname abc.xxx.com --url localhost:1234

```

打开浏览器输入http://abc.xxx.com ，正常已经完成穿透解析。

## 第四步：保证argo tunnel后台运行& 进程守护

使用以下命令让任务后台执行
```
nohup cloudflared tunnel --hostname abc.xxx.com --url localhost:1234 &
```
当然也可以使用supervisor来守护进程
```
[program:cf-tunnel]

command=cloudflared tunnel --hostname abc.xxx.com --url localhost:1234 --no-tls-verify

autorestart=true
startsecs=10
startretries=36
redirect_stderr=true

user=root ; setuid to this UNIX account to run the program
log_stdout=true ; if true, log program stdout (default true)
log_stderr=true ; if true, log program stderr (def false)
logfile=/var/log/cf-tunnel.log ; child log path, use NONE for none; default AUTO


cloudflared login
```
