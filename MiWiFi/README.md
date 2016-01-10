FreeRouter_V2 for MiWiFi
=============
小米路由器跟OpenWRT略有不同

1. 需要改写 `/etc/hotplug.d/iface/70-vpn`
2. 需要在`/etc/ppp/ip-up.d/vpnup.sh`中增加`route del default dev $VPN_DEV`,删除pptp-vpn启动添加的路由，不然全部流量都会走VPN
3. 为避免冲突，`/etc/iproute2/rt_tables` 需要增加`gfw`路由表，因为小米路由器已经存在一个`vpn`路由表
4. `/etc/config/dhcp`中需要增加`option conf-dir '/etc/dnsmasq.d'`
5. `/etc/dnsmasq.d/option.conf`中增加`all-servers`
6. 增加MTU的设置（云梯需要设置1280，否则twitter图片看不了)

## gfw.conf更新
> 增加了根据gfwlist项目，更新域名列表的脚本,考虑到小米路由器cpu还是太弱，这种通过gfwlist转换ipset配置的脚本，我是跑在自己的MBP上，自动push到github，然后小米路由器通过crontab定时更新文件。

```bash
curl -s 'https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt' | base64 -D | grep --color=none -vE '^(!|@@|/|%|\[)' | grep -oE '[a-z0-9]([a-z0-9_\.\-]*[a-z0-9])?\.[a-z]{2,6}' | sort -u | awk '{printf("ipset=/%s/gfw\nserver=/%s/8.8.8.8\n",$0,$0)}' > etc/dnsmasq.d/gfw.conf
```

小米路由器时任务，每天两点半自己更新gwf.conf

```bash
30 2 * * * /usr/bin/wget --no-check-certificate https://raw.githubusercontent.com/davidhoo/FreeRouter_V2/master/MiWiFi/etc/dnsmasq.d/
gfw.conf -O /etc/dnsmasq.d/gfw.conf && /etc/init.d/dnsmasq restart
```
