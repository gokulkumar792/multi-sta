# multi-sta
POSIX shell scripts to simultaneously control multiple wireless STAs created with ath9k driver

## Notes:
- Enable software crypto by setting the module param `nohwcrypt=1` while loading the ath9k driver. Only then the newly created VDEVs would be able to decrypt the packets if connected to a secured network.
- List of dependencies for the scripts
    - wpa_supplicant
    - wpa_cli
    - ip (from iproute2 package)
    - iw

## 1) iface - Control multiple wireless VDEVs
```
USAGE: iface [ARGS]
-o [string]             operation on wireless vdev (add, del, up, down)
-i [number or range]    vdev number or range of numbers (0-2048)
-h                      show this message
```

### Example:

Create new wireless vdevs from wlan1 to wlan10
```
$ ./iface -o add -i 1-10
```
Delete the wireless vdev wlan2
```
$ ./iface -o del -i 2
```
Bring down the wireless vdevs from wlan3 to wlan5
```
$ ./iface -o down -i 3-5
```
Bring up the wireless vdev wlan3
```
$ ./iface -o up -i 3
```

## 2) supplicant - Control the wpa_supplicant running on multiple wireless VDEVs

```
USAGE: supplicant [ARGS]
-o [string]             operation on wpa_supplicant (start, stop, restart, cli)
-i [number or range]    vdev number or range of numbers (0-2048)
-p [file path]          wpa_supplicant conf file path
-c [string]             wpa_cli command
-h                      show this message
```

### Examples:

Start wpa_supplicant on vdevs from wlan1 to wlan10 with /etc/wpa_supplicant.conf file
```
$ ./supplicant -o start -i 1-10 -p /etc/wpa_supplicant.conf
```
Stop the wpa_supplicant running on vdev wlan1
```
$ ./supplicant -o stop -i 1
```
Restart the wpa_supplicant running on vdevs from wlan3 to wlan5 with /etc/wpa_supplicant.conf file
```
$ ./supplicant -o restart -i 3-5 -p /etc/wpa_supplicant.conf
```
Run the wpa_cli command "status" on vdev wlan2
```
$ ./supplicant -o cli -i 2 -c status
```
