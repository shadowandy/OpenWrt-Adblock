### Adblock Package for OpenWrt

Had the idea of building a small dedicated DNS box that resolves known advertisement hostnames to a web server (NGINX on the box itself) that serves 1x1 pixel transparent gif (i.e. empty_gif) and 204 (file not found) for all non-images (i.e. non png, gif, jpe?g). This little box will just be a LAN client to your network. On your router, just fix the DHCP address for this little box and distribute the IP address of the box to the rest of the clients as DNS IP. 

Most of the actual adblock script is adapted from [teffalump's adblock](https://github.com/teffalump/adblock). Have edited the script to build block.hosts into /tmp instead of /etc to alleviate the storage requirement. The adblock.sh has been installed (i.e. has been sh /etc/adblock.sh -f) in the compiled firmware.

Have created a OpenWrt init script (/etc/init.d/adblock) to start/stop the adblock feature.

## Building the Image
**Stable**

Device | Command
:-- | :--
TL-MR3020 | ```make image PROFILE=TLMR3020 PACKAGES="nginx libopenssl -luci -ppp-mod-pppoe -ppp -kmod-pppoe -kmod-pppox -kmod-ppp" FILES=~/OpenWrt-Adblock/files/```
TL-WR703N | ```make image PROFILE=TLWR703 PACKAGES="nginx libopenssl -luci -ppp-mod-pppoe -ppp -kmod-pppoe -kmod-pppox -kmod-ppp" FILES=~/OpenWrt-Adblock/files/```

**Experimental**

Device | Command
:-- | :--
TL-MR3020 (VPN) | ```make image PROFILE=TLMR3020 PACKAGES="lighttpd lighttpd-mod-rewrite lighttpd-mod-redirect lighttpd-mod-fastcgi php5-fastcgi libopenssl pptpd kmod-mppe wget -wget-nossl -luci -ppp-mod-pppoe -ppp -kmod-pppoe -kmod-pppox -kmod-ppp -kmod-ath -kmod-ath9k -kmod-ath9k-common" FILES=~/GitHub/OpenWrt-Adblock/files/experimental/```
TL-WR703N (VPN) | ```make image PROFILE=TLWR703 PACKAGES="lighttpd lighttpd-mod-rewrite lighttpd-mod-redirect libopenssl pptpd kmod-mppe wget +lua +liblua -wget-nossl -luci -ppp-mod-pppoe -ppp -kmod-pppoe -kmod-pppox -kmod-ppp -kmod-ath -kmod-ath9k -kmod-ath9k-common" FILES=~/GitHub/OpenWrt-Adblock/files/experimental/```
TL-MR3020 | ```make image PROFILE=TLMR3020 PACKAGES="nginx wget ddns-scripts -wget-nossl -luci -ppp-mod-pppoe ppp -kmod-pppoe -kmod-pppox -kmod-ppp -kmod-ath -kmod-ath9k -kmod-ath9k-common" FILES=~/GitHub/OpenWrt-Adblock/files/experimental/```
TL-WR703N | ```make image PROFILE=TLWR703 PACKAGES="nginx wget ddns-scripts -wget-nossl -luci -ppp-mod-pppoe ppp -kmod-pppoe -kmod-pppox -kmod-ppp -kmod-ath -kmod-ath9k -kmod-ath9k-common" FILES=~/GitHub/OpenWrt-Adblock/files/experimental/```

*Do remember to remove the /etc/shadow if you are compiling your own OpenWrt image.*

## Issues

The NGINX package does not support ssl_certificate and ssl_certificate_key directive so it will not be able to correctly establish SSL communication for serving empty_gif and 204.

## To do
- Remove wireless driver and associated kmod to free up storage space for PPTP server and luci (selected)
- Remove telnetd (?)
- Hardcode 8.8.8.8 and 4.4.4.4 as DNS server to avoid DNS loops

```
server.modules += ( "mod_rewrite", "mod_magnet" )

url.rewrite-once = ( 
    ".+?(png|gif|jpe?g)$" => "/empty.gif",
    "!.+?empty.gif$" => /204
)
$HTTP["url"] == "/204" {
    magnet.attract-raw-url-to = ( "/etc/204.lua" )
}

##### 204.lua #####
return 204
```
