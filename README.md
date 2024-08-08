# notes-wireguard

Using ifupdown instead of wg-quick, config is wg1.conf


## server : /etc/wireguard/wg1.conf

[Interface]
PrivateKey = <server private key>
ListenPort = 59653

[Peer]

PublicKey = <peer public key> 
AllowedIPs = 10.66.15.2
# note AllowedIPs should only be the tunnel IP of the client



## server : /etc/network/interfaces


auto wg1
iface wg1 inet static
address 10.66.15.1
netmask 255.255.255.0
pre-up ip link add $IFACE type wireguard
pre-up wg setconf $IFACE /etc/wireguard/$IFACE.conf
post-down ip link del $IFACE 



