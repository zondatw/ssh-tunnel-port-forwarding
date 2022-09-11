# Port forwarding via ssh tunnel

## Pre-setup

### SSH

```shell
cd docker
make
```

use `vim` to modify some ssh setting.  

```
# vim /etc/ssh/sshd_config
Port 2212
PasswordAuthentication yes
PermitRootLogin yes
```

After save, restart sshd  

```shell
/etc/init.d/ssh restart
```

and you need to set password for root  

```shell
passwd
```

## Fowarding

### Case 1

User on the client, that's using port 9999 can connect remote http server which port is 8088 and only for localhost use.  

```shell
| ------ |    | --------------------------------------- |
| Client | -> | Server(Docker) -> localhost http server |
| (9999) |    |     (2212)              (8088)          |
| ------ |    | --------------------------------------- |
``````

#### Server setup

```shell
python3 -m http.server --bind 127.0.0.1 8088
```

you can connect [http://127.0.0.1:8088](http://127.0.0.1:8088) to check server available  

#### Client setup

The command mean:  
1. client port: 9999
2. target url on server: localhost
3. target port on server: 8088
4. server ssh port: 2212
5. server uri: root@localhost

```shell
ssh -L 9999:localhost:8088 -p 2212 root@localhost
```

you also can connect [http://127.0.0.1:9999](http://127.0.0.1:9999) to check success  

### Case 2

User on the internet, that's using port 8099 can connect local server which port is 9900 and only for localhost use.  

```shell
| ------ |    | ---------------------------- |    | ---- |
| Client | <- | ssh server <- Server(Docker) | <- | User |
| (9900) |    |   (2212)         (8099)      |    |      |
| ------ |    | ---------------------------- |    | ---- |
``````

#### Client setup


```shell
python3 -m http.server --bind 127.0.0.1 9900
```

you can connect [http://127.0.0.1:9900](http://127.0.0.1:9900) to check server available  

#### Server setup

you need to open Gateway setting of sshd.  

```
# vim /etc/ssh/sshd_config
GatewayPorts yes
```

and restart sshd  

The command mean:  
1. client url: localhost
2. client port: 9900
3. server outside url: 0.0.0.0
4. server outside port: 8099
5. server ssh port: 2212
6. server uri: root@localhost

```shell
ssh -R 0.0.0.0:8099:localhost:9900 -p 2212 root@localhost
```

you also can connect [http://127.0.0.1:8099](http://127.0.0.1:8099) to check success  

## Reference

[SSH Tunneling (Port Forwarding) 詳解](https://johnliu55.tw/ssh-tunnel.html)  
[如何利用SSH Tunnel來穿透遠端主機的防火牆，連接到某個服務？](https://magiclen.org/ssh-tunnel/)  
[\[教學\] 透過 SSH Tunnel 將伺服器內部服務綁定到本機電腦](https://xenby.com/b/269-%E6%95%99%E5%AD%B8-%E9%80%8F%E9%81%8E-ssh-tunnel-%E5%B0%87%E4%BC%BA%E6%9C%8D%E5%99%A8%E5%85%A7%E9%83%A8%E6%9C%8D%E5%8B%99%E7%B6%81%E5%AE%9A%E5%88%B0%E6%9C%AC%E6%A9%9F%E9%9B%BB%E8%85%A6%E4%B8%8A)  
[用 ssh tunnel 連線到 router 背後的機器，並建立 SOCKS5 proxy 存取內網資源](https://hackmd.io/@DailyOops/ssh-reverse-tunnel-behind-the-router-with-socks5-proxy)  
