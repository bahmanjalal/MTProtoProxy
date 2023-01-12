# Async MTProto Proxy #
***Way 1 :***

**Autostarting on Boot**

1. `git clone -b stable https://github.com/bahmanjalal/mtprotoproxy.git /opt/mtprotoproxy; cd /opt/mtprotoproxy`
2. *(optional, recommended)* edit *config.py*, set **PORT**, **USERS** and **AD_TAG**
3. In *config.py* secret key is :
```
USERS={
    "tg": "00000000000000000000000000000001"
}
```
And you most change it to another secret key with 32 character length. In Telegram add "***ee***" to begin of line and add "***676f6f676c652e636f6d***" (google.com) to end of line.

4. Create tgproxy user: `useradd --no-create-home -s /usr/sbin/nologin tgproxy`
5. *(optional, recommended)* install cryptography Python module
6. *(optional)* install uvloop Python module
7. Create file **/etc/systemd/system/mtprotoproxy.service** with the following content:

```
[Unit]
    Description=Async MTProto proxy for Telegram
    After=network-online.target
    Wants=network-online.target

[Service]
    ExecStart=/opt/mtprotoproxy/mtprotoproxy.py
    AmbientCapabilities=CAP_NET_BIND_SERVICE
    LimitNOFILE=infinity
    User=tgproxy
    Group=tgproxy
    Restart=on-failure

[Install]
    WantedBy=multi-user.target
```
***If not work above code*** do this :

**nano /opt/mtprotoproxy/mt.sh**

add this code :

```
#!/bin/bash
/opt/mtprotoproxy/
sudo python3 mtprotoproxy.py
```
then:
```
chmod +x /opt/mtprotoproxy/mt.sh
```
***Make service file***
```
nano /etc/systemd/system/mtp.service
```

add this :

```
Description=Description for sample script goes here
After=network.target

[Service]
Type=simple
ExecStart=/opt/mtprotoproxy/mt.sh
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

8. Enable autostarting on boot: `systemctl enable mtprotoproxy`
9. (optional, start the proxy) `systemctl start mtprotoproxy`
10. (optional, get a link to share the proxy) `journalctl -u mtprotoproxy | cat`
  
  
    
-----------------------------------------------------------------------
  
  
    
***Way 2 :***

Fast and simple to setup MTProto proxy written in Python.

## Starting Up ##
    
1. `git clone -b stable https://github.com/bahmanjalal/mtprotoproxy.git; cd mtprotoproxy`
2. *(optional, recommended)* edit *config.py*, set **PORT**, **USERS** and **AD_TAG**
3. `docker-compose up -d` (or just `python3 mtprotoproxy.py` if you don't like Docker)
4. *(optional, get a link to share the proxy)* `docker-compose logs`

![Demo](https://alexbers.com/mtprotoproxy/install_demo_v2.gif)

## Channel Advertising ##

To advertise a channel get a tag from **@MTProxybot** and put it to *config.py*.

## Performance ##

The proxy performance should be enough to comfortably serve about 4 000 simultaneous users on
the VDS instance with 1 CPU core and 1024MB RAM.

## More Instructions ##

- [Running without Docker](https://github.com/alexbers/mtprotoproxy/wiki/Running-Without-Docker)
- [Optimization and fine tuning](https://github.com/alexbers/mtprotoproxy/wiki/Optimization-and-Fine-Tuning)

## Advanced Usage ##

The proxy can be launched:
- with a custom config: `python3 mtprotoproxy.py [configfile]`
- several times, clients will be automaticaly balanced between instances
- with uvloop module to get an extra speed boost
- with runtime statistics exported to [Prometheus](https://prometheus.io/)
