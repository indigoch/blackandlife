# Ubuntu搭建pptp服务

1. 安装 PPTPD

    ```
    apt install pptpd
    ```

2. 编辑 pptpd.conf文件

    ```
    vi /etc/pptpd.conf

    取消注释下面内容

    localip 192.168.0.1
    remoteip 192.168.0.234-238,192.168.0.245
    ```

3. 添加用于登陆的账户

    ```
    vim /etc/ppp/chap-secrets

    格式如下：

    # client server secret IP addresses
    cqc33 pptpd 123456ll.. *

    从左到右依次是用户名，协议(填写pptpd)，密码，自己指定。IP，填*即可。中间用空格分别隔开。
    ```

4. 设置DNS解析，编辑pptpd-options文件

    ```
    vi /etc/ppp/pptpd-options

    找到ms-dns，取消掉注释，并修改DNS地址，推荐用 Google DNS 8.8.8.8 和 8.8.4.4 更改为如下内容

    ms-dns 8.8.8.8
    ms-dns 8.8.4.4
    ```

5. 开启转发

    ```powershell
    vi /etc/sysctl.conf

    取消注释以下内容

    net.ipv4.ip_forward=1

    这句话意思是：打开内核IP转发 更新一下配置

    sudo sysctl -p
    ```

6. 安装iptables并设置

    ```powershell
    apt install iptables
    sudo iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o eth0 -j MASQUERADE

    后面这句话作用是：立刻让LINUX支持NAT(platinum) 或者使用下面这句话来代替

    iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
    这种就比较简单，自动伪装ip 如果VPN服务器是用来代理上网的，仅有
    上面的配置会出现访问网站缓慢的情况，需要手动修改一下转发包的mss

    iptables -A FORWARD -p tcp --syn -s 10.0.0.0/24 -j TCPMSS --set-mss 1356
    这样就不会出现打不开网页的问题了，最后可以保存一下防火墙。

    iptables-save
    ```

7. 重新启动服务

    ```powershell
    /etc/init.d/pptpd restart
    ```