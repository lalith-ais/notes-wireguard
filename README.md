# notes-wireguard

Using ifupdown instead of wg-quick, config is wg1.conf

## server
### generate keys 
<pre>
  umask 077
  wg genkey | tee privatekey | wg pubkey > publickey
</pre>

### /etc/wireguard/wg1.conf
<pre>
[Interface]
PrivateKey = privatekey generated above
ListenPort = 59653

[Peer]

PublicKey = peer's publickey
AllowedIPs = 10.66.15.2
#### note AllowedIPs should only be the tunnel IP of the client
</pre>

add more peers to expand the network. Each peer must have a unique static ip 

###  /etc/network/interfaces

<pre>
auto wg1
iface wg1 inet static
address 10.66.15.1
netmask 255.255.255.0
pre-up ip link add $IFACE type wireguard
pre-up wg setconf $IFACE /etc/wireguard/$IFACE.conf
post-down ip link del $IFACE 
</pre>



### bring up tunnel
each time wg1 is edited use ifdown / ifup commnds to reread config
  <pre>
    ifdown wg1
    ifup wg1
  </pre>

  

## peer
### generate keys 
<pre>
  umask 077
  wg genkey | tee privatekey | wg pubkey > publickey
</pre>

### create config wg0.conf
<pre>
[Interface]
Address = 10.66.15.2/24
PrivateKey = privatekey generate above for client
ListenPort = 21842 - any random port

[Peer]
PublicKey = public key of the server
Endpoint = serverip:59653 <- port from server config
AllowedIPs = 0.0.0.0/0 <- this will make tunnel the default route
PersistentKeepalive = 25
</pre>

### create QR code for android app

<pre>
  qrencode -t ansiutf8 < wg0.conf 
</pre>




