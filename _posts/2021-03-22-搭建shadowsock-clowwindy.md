# 搭建shadowsock-clowwindy

## [shadowsocks](https://github.com/shadowsocks/shadowsocks/wiki)使用说明

一个可穿透防火墙的快速代理

## 服务端

### 安装

服务端

- 单行运行

```shell
apt install -y python3-pip
apt install -y vim
pip3 install https://github.com/shadowsocks/shadowsocks/archive/master.zip
```

### 通过配置文件进行配置

创建一个文件

```json
{
    "server":"0.0.0.0",
    "server_port":1080,
    "local_address": "127.0.0.1",
    "local_port":1360,
    "password":"blackandlife.tk",
    "timeout":300,
    "method":"xchacha20-ietf-poly1305",
    "fast_open": true
}
```

### 字段说明

|    server     |    服务器地址    |
| :-----------: | :--------------: |
|  server_port  |    服务器端口    |
| local_address |   本机监听地址   |
|  local_port   |   本机监听端口   |
|   password    |       密码       |
|    timeout    |     超时毫秒     |
|    method     |     加密方式     |
|   fast_open   | 使用tcp_fastoren |

		### 开启tcp_fastoren

on Debian7:

```shell
vi /etc/sysctl.d/local.conf
```



``` json
# max open files
fs.file-max = 51200
# max read buffer
net.core.rmem_max = 67108864
# max write buffer
net.core.wmem_max = 67108864
# default read buffer
net.core.rmem_default = 65536
# default write buffer
net.core.wmem_default = 65536
# max processor input queue
net.core.netdev_max_backlog = 4096
# max backlog
net.core.somaxconn = 4096

# resist SYN flood attacks
net.ipv4.tcp_syncookies = 1
# reuse timewait sockets when safe
net.ipv4.tcp_tw_reuse = 1
# turn off fast timewait sockets recycling
net.ipv4.tcp_tw_recycle = 0
# short FIN timeout
net.ipv4.tcp_fin_timeout = 30
# short keepalive time
net.ipv4.tcp_keepalive_time = 1200
# outbound port range
net.ipv4.ip_local_port_range = 10000 65000
# max SYN backlog
net.ipv4.tcp_max_syn_backlog = 4096
# max timewait sockets held by system simultaneously
net.ipv4.tcp_max_tw_buckets = 5000
# turn on TCP Fast Open on both client and server side
net.ipv4.tcp_fastopen = 3
# TCP receive buffer
net.ipv4.tcp_rmem = 4096 87380 67108864
# TCP write buffer
net.ipv4.tcp_wmem = 4096 65536 67108864
# turn on path MTU discovery
net.ipv4.tcp_mtu_probing = 1

# for high-latency network
net.ipv4.tcp_congestion_control = hybla

# for low-latency network, use cubic instead
# net.ipv4.tcp_congestion_control = cubic
```

执行系统配置

```shell
sysctl --system
```

### 加密方式

安装 m2crypto会使加密更快

```shell
apt install -y python3-m2crypto
```

如果要使用salsa20和chacha20要安装 libsodium

```shell
apt install -y python3-m2crypto
```

如果要使用salsa20和chacha20要安装 libsodium

```shell
sudo apt-get install build-essential
wget https://github.com/jedisct1/libsodium/releases/download/1.0.18-RELEASE/libsodium-1.0.18.tar.gz
tar xf libsodium-1.0.18.tar.gz && cd libsodium-1.0.18
./configure && make -j2
sudo make install
sudo ldconfig
```

## 后台运行

```shell
ssserver -c /etc/shadowsocks.json -d start
ssserver -c /etc/shadowsocks.json -d stop
```

