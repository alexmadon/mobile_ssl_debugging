@title[Introduction]

#### Alex <span class="gold">Madon</span>
#### capturing Android SSL traffic
#### 2017/11/16

---

@title[SSL]

![Image-Absolute](assets/ssl_protocol_alex.png)

---

@title[On Desktop]

###  <span class="gold">On Desktop</span>
###  wireshark + private key
###  wireshark + SSLKEYLOGFILE

---

@title[On Android: iptables+mitmproxy -T+pptpd]


### On Android: iptables+mitmproxy -T+pptpd

![Image-Absolute](assets/alex_proxy.svg.png)

---

@title[pptpd]

```shell
apt-get install pptpd

Edit /etc/pptpd.conf and add:

localip 5.9.136.58
remoteip 10.68.68.1-62
```

@[1](install )
@[5](Linux server)
@[6](Android IP)

---

@title[pptpd-options]

```shell
# egrep -v ’^#.*’ /etc/ppp/pptpd-options | egrep -v ’^$’
name madon.net
refuse-pap
refuse-chap
refuse-mschap
require-mschap-v2
require-mppe-128
nomppe-40
nodefaultroute
debug
dump
lock
nobsdcomp
auth
```

---

@title[pptpd-options]

```shell
/etc/ppp/chap-secrets
# Secrets for authentication using CHAP
# client server secret IP addresses
MYUSERNAME * MYPASSWORD *

# systemctl restart pptpd
```


---

@title[iptables]

```shell
iptables -A INPUT -p tcp --dport 1723 -j ACCEPT
iptables -A INPUT -p gre -j ACCEPT

sysctl -w net.inet.ip.forwarding=1
# (or echo 1 > /proc/sys/net/ipv4/ip_forward )

iptables -t nat -A PREROUTING -p tcp --destination-port 80 -j REDIRECT --to-ports 8080
iptables -t nat -A PREROUTING -p tcp --destination-port 443 -j REDIRECT --to-ports 8080

iptables -A INPUT -p tcp --dport 80 -s 10.68.68.1 -j ACCEPT
iptables -A INPUT -p tcp --dport 8080 -s 10.68.68.1 -j ACCEPT
```

---

@title[mitmproxy]

```shell
mitmdump -T -v
Proxy server listening at http://0.0.0.0:8080
10.68.68.234:56061: clientconnect
10.68.68.234:56061: Establish TLS with client
10.68.68.234:56061: Client Handshake failed. The client may not trust the proxy’s certificate for
10.68.68.234:56061: ClientHandshakeException(’Cannot establish TLS with client (sni: alt3-mtalk.go
10.68.68.234:56061: clientdisconnect
```

---

@title[CA mitmproxy]

```shell
~/.mitmproxy # ls -1
mitmproxy-ca-cert.cer
mitmproxy-ca-cert.p12
mitmproxy-ca-cert.pem
mitmproxy-ca.pem
mitmproxy-dhparam.pem
```

---

@title[mitmproxy -T]

```shell
# mitmdump -T -v
Proxy server listening at http://0.0.0.0:8080
10.68.68.1:57422: clientconnect
10.68.68.1:57422: serverconnect
-> 50.19.96.52:443
10.68.68.1:57422: Establish TLS with server
10.68.68.1:57422: ALPN selected by server: -
10.68.68.1:57422: Establish TLS with client
10.68.68.1:57422: ALPN for client: b’http/1.1’
```

---

@title[mitmproxy python API]


```shell
http://docs.mitmproxy.org/en/stable/scripting/api.html

https://github.com/mitmproxy/mitmproxy/tree/master/examples/simple


mitmdump -T -v -w dump1.txt

python3 io_read_dumpfile.py dump1.txt 

<HTTPFlow
  request = Request(GET 162.13.248.112:443/m/rpc/log/pageload?a=mserpjsv&mobtk=1bv24lrkl14gu38k&atk=1bv24lrkp14gu48m&scrWidth=1080&scrHeight=1920&pixelRatio=3&scrO
  ```

  
---

Questions?

---

Thank you
