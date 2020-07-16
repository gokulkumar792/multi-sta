# multi-sta
POSIX shell scripts to simultaneously control multiple wireless STAs created with ath9k (or) mac80211_hwsim driver

## Notes:

For ath9k:
- Enable software crypto by setting the module param `nohwcrypt=1` while loading the ath9k driver. Only then the newly created VDEVs would be able to decrypt the packets if connected to a secured network.

For both ath9k & mac80211_hwsim:
- List of dependencies for the scripts
    - wpa_supplicant
    - wpa_cli
    - ip (from iproute2 package)
    - iw

## 1) iface - Control multiple wireless VDEVs
```
USAGE: iface [ARGS]
-o [string]             operation on wireless vdev (add, del, up, down)
-O [string]             OUI to be used while selecting a MAC addr for the new vdev (optional)
-p [number]             pdev number
-i [number or range]    vdev number or range of numbers (0-2048)
-h                      show this message
```

### Example:

Create new wireless vdevs from phy0-wlan1 to phy0-wlan10
```
$ ./iface -o add -p 0 -i 1-10
```
Create new wireless vdev phy0-wlan20 with OUI aa:aa:aa
```
$ ./iface -o add -O aa:aa:aa -p 0 -i 20
```
Delete the wireless vdev phy0-wlan2
```
$ ./iface -o del -p 0 -i 2
```
Bring down the wireless vdevs from phy0-wlan3 to phy0-wlan5
```
$ ./iface -o down -p 0 -i 3-5
```
Bring up the wireless vdev phy0-wlan3
```
$ ./iface -o up -p 0 -i 3
```

## 2) supplicant - Control the wpa_supplicant running on multiple wireless VDEVs

```
USAGE: supplicant [ARGS]
-o [string]             operation on wpa_supplicant (start, stop, restart, cli)
-p [number]             pdev number
-i [number or range]    vdev number or range of numbers (0-2048)
-c [file path]          wpa_supplicant conf file path
-C [string]             wpa_cli command
-h                      show this message
```

### Examples:

Start wpa_supplicant on vdevs from phy0-wlan1 to phy0-wlan10 with /etc/wpa_supplicant.conf file
```
$ ./supplicant -o start -p 0 -i 1-10 -c /etc/wpa_supplicant.conf
```
Stop the wpa_supplicant running on vdev phy0-wlan1
```
$ ./supplicant -o stop -p 0 -i 1
```
Restart the wpa_supplicant running on vdevs from phy0-wlan3 to phy0-wlan5 with /etc/wpa_supplicant.conf file
```
$ ./supplicant -o restart -p 0 -i 3-5 -c /etc/wpa_supplicant.conf
```
Run the wpa_cli command "status" on vdev phy0-wlan2
```
$ ./supplicant -o cli -p 0 -i 2 -C status
```
