# clash-for-linux
There is a way about how to set a clash for you linux

由于任务需求，经常需要在不同版本的ubuntu系统中来回切换，但是又要常常用到梯子，深感繁琐。因此在这里记录一下自己的配置过程，以待参考。

一、下载clash
大部分地址已经失效，所以这一步请读者自行寻找下载链接。

二、配置系统并尝试运行

su

mkdir clash

cd Dowland

将下载的压缩包解压

mv Dowland/clash-linux-amd64-v1.18.0 /home/ros/clash

cd

cd clash

导入订阅文件 有时候导入失败，这里提供两种方法：

方法一：
wget -O config.yaml [订阅链接]

 wget -O Country.mmdb https://www.sub-speeder.com/client-download/Country.mmdb

方法二：如果已经在windows内配置成功，找到config.yaml文件，coutry.mmdb与cache.db文件，通过filezila或者邮箱等途径传给ubuntu的clash目录内


chmod +x clash 

ll

ros@ros-virtual-machine:~/clash$ ll

总用量 15572
drwxr-xr-x  2 root root    4096 5月  26 23:14 ./
drwxr-xr-x 31 ros  ros     4096 5月  26 23:11 ../
-rw-rw-r--  1 ros  ros    32768 5月  26 22:15 cache.db
-rwxrwxr-x  1 ros  ros  9904128 5月  26 22:21 clash*
-rw-rw-r--  1 ros  ros    63157 5月  26 22:16 config.yaml
-rw-rw-r--  1 ros  ros  5932534 5月  26 22:16 Country.mmdb

 ./clash -d .
 
INFO[0000] Start initial compatible provider Proxy      
INFO[0000] Start initial compatible provider Domestic   
INFO[0000] Start initial compatible provider AsianTV    
INFO[0000] Start initial compatible provider Others     
INFO[0000] Start initial compatible provider GlobalTV

三、开启代理

打开系统设置，点击网络代理右边的⚙按钮，选择手动，填写HTTP和HTTPS代理为127.0.0.1:7890，填写Socks主机为127.0.0.1:7891，即可启用系统代理，如下图所示。
![image](https://github.com/user-attachments/assets/7da88135-5dec-4e9d-948f-616c3f7e4cfc)



这个时候一般就成功翻上了，可以Ping一下youtube试试能不能ping通

四、配置开机自启动

sudo su

cd

touch /etc/systemd/system/clash.service

vi /etc/systemd/system/clash.service

填入以下内容

[Unit]

Description=Clash service

After=network.target

[Service]

Type=simple

User=ros（你的用户名）

ExecStart=/home/ros/clash/clash（Clash 程序路径）

Restart=on-failure

RestartPreventExitStatus=23

[Install]

WantedBy=multi-user.target

wq保存出来

# 重载服务
sudo systemctl daemon-reload
# 开机启动
sudo systemctl enable clash
# 启动服务
sudo systemctl start clash
# 查看服务状态
sudo systemctl status clash

都跑一遍，然后服务状态如下则表示配置成功

root@ros-virtual-machine:~# systemctl status clash.service
● clash.service - Clash service
   Loaded: loaded (/etc/systemd/system/clash.service; enabled; vendor preset: en
   Active: active (running) since Sun 2024-05-26 23:19:39 CST; 7s ago
 Main PID: 3932 (clash)
    Tasks: 7 (limit: 14426)
   CGroup: /system.slice/clash.service
           └─3932 /home/ros/clash/clash

5月 26 23:19:39 ros-virtual-machine systemd[1]: Started Clash service.
5月 26 23:19:39 ros-virtual-machine clash[3932]: time="2024-05-26T23:19:39+08:00


#更新
在git中使用代理

当你使用了各种代理软件访问诸如Github、Google、油管、推特这些网址，你会发现基本可以访问，只不过是访问速度不同，但是不管你使用什么代理软件，你的git clone指令从Github远程库下载库的速度都不会受到影响。

当使用代理软件访问诸如Github、Google、YouTube和Twitter等网站时，代理软件会通过中继服务器来转发你的网络请求。代理服务器可以帮助绕过网络封锁、提供更快的访问速度或提供匿名性。

然而，对于Git克隆命令（例如`git clone`），它是使用Git协议进行通信的，而不是使用常见的HTTP或HTTPS协议。Git协议使用的是自己的网络端口（默认为9418），而不是HTTP（端口80）或HTTPS（端口443）。

因此，当使用代理软件时，代理服务器可能只会处理HTTP和HTTPS流量，并不会拦截或修改Git协议的流量。这就是为什么你观察到无论使用什么代理软件，Git克隆命令从Github远程库下载的速度都不受影响的原因。

需要注意的是，代理软件通常会影响其他基于HTTP或HTTPS的网络请求的速度，因为它们会介入并中继这些请求。但是对于Git克隆命令，由于它使用Git协议，代理软件并不会干涉其通信流量。

这时候就要改变Git本身的配置，让git的默认端口号和你代理服务器的监听端口号一样。

因此我们之前设置的监听端口号分别为 7890 和 7891 那么

设置代理

git config --global http.proxy 代理地址

git config --global https.proxy 代理地址

格式如下
![image](https://github.com/user-attachments/assets/351cbe40-e4e9-4800-b92e-5a73c65f5f13)

取消代理

git config --global --unset http.proxy

git config --global --unset https.proxy

![image](https://github.com/user-attachments/assets/fe779732-2027-4c04-b0ae-f8192c4d81f1)

查看当前已设置的代理

git config --global --get http.proxy

git config --global --get https.proxy

查询的时候没有打印任何东西证明没有设置代理

