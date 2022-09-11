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
| ------ |   | ---------------------------------------- |
| Client |-> | Server(Docker)  -> localhost http server |
| (9999) |   |     (2212)              (8088)           |
| ------ |   | ---------------------------------------- |
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
