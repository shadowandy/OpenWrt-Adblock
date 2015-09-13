### Adblock Package for OpenWrt

Had the idea of building a small dedicated DNS box that resolves known advertisement hostnames to a web server (NGINX on the box itself) that serves 1x1 pixel transparent gif (i.e. empty_gif) and 204 (file not found) for all non-images (i.e. non png, gif, jpe?g). This little box will just be a LAN client to your network. On your router, just fix the DHCP address for this little box and distribute the IP address of the box to the rest of the clients as DNS IP. 

Most of the actual adblock script is adapted from [teffalump's adblock](https://github.com/teffalump/adblock). Have edited the script to build block.hosts into /tmp instead of /etc to alleviate the storage requirement. The adblock.sh has been installed (i.e. has been sh /etc/adblock.sh -f) in the compiled firmware.

Have created a OpenWrt init script (/etc/init.d/adblock) to start/stop the adblock feature.

## Building the Image

>Device | Command
>:- | :-
TL-MR3020 | make image PROFILE=TLMR3020 PACKAGES="nginx libopenssl -luci -ppp-mod-pppoe -ppp -kmod-pppoe -kmod-pppox -kmod-ppp" FILES=~/OpenWrt-Adblock/files/
TL-WR703N | make image PROFILE=TLWR703N PACKAGES="nginx libopenssl -luci -ppp-mod-pppoe -ppp -kmod-pppoe -kmod-pppox -kmod-ppp" FILES=~/OpenWrt-Adblock/files/
