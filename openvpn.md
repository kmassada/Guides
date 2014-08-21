## install copy files
```shell
yum install openvpn easy-rsa -y
cp /usr/share/doc/openvpn-*/sample/sample-config-files/server.conf /etc/openvpn/
```

`mkdir -p /var/log/openvpn/`

## config
```shell
grep ^[^#\;] /etc/openvpn/server.conf
```

>port 1194
>proto udp
>dev tun
>ca ca.crt
>cert vpnserver.crt
>key vpnserver.key  # This file should be kept secret
>dh dh2048.pem
>server 10.8.0.0 255.255.255.0
>ifconfig-pool-persist ipp.txt
>push "route 192.168.0.0 255.255.255.0"
>push "redirect-gateway def1 bypass-dhcp"
>push "dhcp-option DNS 8.8.8.8"
>push "dhcp-option DNS 8.8.4.4"
>client-to-client
>topology subnet
>keepalive 10 120
>tls-auth ta.key 0 # This file is secret
>comp-lzo
>user nobody
>group nobody
>persist-key
>persist-tun
>status /var/log/openvpn/openvpn-status.log
>log         /var/log/openvpn/openvpn.log
>log-append  /var/log/openvpn/openvpn.log
>verb 5
>mute 5

## rsa keys
```shell
mkdir -p /etc/openvpn/easy-rsa/keys
cp -rf /usr/share/easy-rsa/2.0/* /etc/openvpn/easy-rsa/
cat /etc/openvpn/easy-rsa/vars
```

### rsa values
>export KEY_COUNTRY=”US”
>export KEY_PROVINCE=”MD”
>export KEY_CITY=”CharmCity”
>export KEY_ORG=”PAD”
>export KEY_EMAIL=”admn@domain.com”
>export KEY_NAME=vpnserver
>export KEY_OU=servers

### ssl
```shell
cp openssl-1.0.0.cnf openssl.cnf
source ./vars
./clean-all
```

### build keys
`./build-ca`
>CN: pad ca
>Name: vpnserver

`./build-key-server vpnserver`
>CN: vpnserver
>Name: vpnserver

`./build-key 700Z5A`
>CN: 700Z5A
>Name: vpnserver

### gen keys
```shell
./build-dh
cd /etc/openvpn/easy-rsa
cp keys/ca.crt /etc/openvpn/
cp keys/dh2048.pem /etc/openvpn/                          
cp keys/vpnserver.{crt,key} /etc/openvpn/  
openvpn --genkey --secret /etc/openvpn/ta.key
```

### revoking
```shell
. /etc/openvpn/easy-rsa/2.0/vars
. /etc/openvpn/easy-rsa/2.0/revoke-full client
```

### copy to client
```shell
cd /etc/openvpn/easy-rsa/keys 
scp ca.crt 700Z5A.{key,crt} ../../ta.key st34lth@192.168.1.7:~/
```

## ip traffic
`vi /etc/sysctl.conf`
>Controls IP packet forwarding
> net.ipv4.ip_forward = 1

`sysctl -p`

### routing
`iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o br0 -j MASQUERADE`

#### openvpn port
`iptables -I INPUT -p udp -m udp --dport 1194 -j ACCEPT`

#### from and to interface routing
```shell
iptables -A FORWARD -i tun0 -j ACCEPT 
iptables -A FORWARD -i br0 -o tun0 -m state --state RELATED,ESTABLISHED -j ACCEPT 
iptables -A FORWARD -i tun0 -o br0 -m state --state RELATED,ESTABLISHED -j ACCEPT
```

#### restart
```shell
service iptables restart
service openvpn start
chkconfig openvpn on
```

## Troubleshooting
notes

### [server side]
#### turn off selinux
```shell
setenforce 0
grep ^[^#] /etc/openvpn/server.conf 
```

#### replace enabled by disabled
```shell
vi /etc/selinux
reboot
```

#### netstat monitoring for traffic
##### find traffic on interface
`netstat -t -u -Ibr0`

##### routing table
`netstat -r`

##### tcp/udp connections on server
`netstat -t -u -p`

##### list of ports listening
`netstat -t -u -l`

### [client side]
`netstat -rn`

`traceroute google.com`

`ping 8.8.8.8`

`ifconfig`