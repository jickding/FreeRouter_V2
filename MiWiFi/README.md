FreeRouter_V2 for MiWiFi
=============
小米路由器跟OpenWRT略有不同

1. 需要改写 `/etc/hotplug.d/iface/70-vpn`
2. 需要在`/etc/ppp/ip-up.d/vpnup.sh`中增加`oute del default dev $VPN_DEV`,删除pptp-vpn启动添加的路由，不然全部流量都会走VPN
3. 为避免冲突，`/etc/iproute2/rt_tables` 需要增加`gfw`路由表，因为小米路由器已经存在一个`vpn`路由表
4. `/etc/config/dhcp`中需要增加`option conf-dir '/etc/dnsmasq.d'`
5. `/etc/dnsmasq.d/option.conf`中增加`all-servers`
