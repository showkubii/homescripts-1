# WIREHGUARD.MD

This is my configuration and setup for my Wireguard client on my Linux box. I setup one additional IP address and use that IP to bind to my specific clients and only that client gets routed through the VPN.

## Installation

```bash
sudo su - 
apt install wireguard
wg-quick up wg0
cd /etc/wireguard
edit wg0.conf for your provider
systemctl enable wg-quick@wg0
wg0 show
```
As root, you need to enable forwarding in /etc/sysctl.conf.

```bash
# Torguard VPN
net.ipv4.ip_forward=1
```

You run "sysctl -p" to enable the forwarding change.


My configuration file for wg0.conf looks like this as I use 192.168.1.31 for my VPN only IP.

```bash
# TorGuard WireGuard Config
[Interface]
PrivateKey = PRIVATEKEYHERE
ListenPort = 51820
Address = 10.13.76.217/24
DNS = 8.8.8.8
Table = off
# I use 192.168.1.31 for my additional IP. I use TCP port 42345 for my port forward. 
# This allows the routing for .31 through the VPN and redirects the port forward to the
# internal IP.
PostUp = ip rule add from 192.168.1.31 table 42; ip route add default dev wg0 table 42; iptables -t nat -A POSTROUTING -o wg0 -j MASQUERADE;iptables -t nat -A PREROUTING -p tcp --dport 42345 -j DNAT --to 192.168.1.31
PostDown = ip rule del from 192.168.1.31 table 42; iptables -t nat -D POSTROUTING -o wg0 -j MASQUERADE;iptables -t nat -D PREROUTING -p tcp --dport 42345 -j DNAT --to 192.168.1.31
[Peer]
PublicKey = PUBLICKEYHERE
AllowedIPs = 0.0.0.0/0
Endpoint = 67.213.221.19:1443
PersistentKeepalive = 25
```

## Checking IP

```bash
curl ipinfo.io
# This should return your normal WAN IP information
#
curl ipinfo.io --interface 192.168.1.31
# This should return your VPN provider information
