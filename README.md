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

Create new wireless vdevs from phy0-sta1 to phy0-sta10
```
$ ./iface -o add -p 0 -i 1-10
```
Create new wireless vdev phy0-sta20 with OUI aa:aa:aa
```
$ ./iface -o add -O aa:aa:aa -p 0 -i 20
```
Delete the wireless vdev phy0-sta2
```
$ ./iface -o del -p 0 -i 2
```
Bring down the wireless vdevs from phy0-sta3 to phy0-sta5
```
$ ./iface -o down -p 0 -i 3-5
```
Bring up the wireless vdev phy0-sta3
```
$ ./iface -o up -p 0 -i 3
```

## 2) supplicant - Control the wpa_supplicant(s) which is/are managing the multiple wireless VDEVs

```
USAGE: supplicant [ARGS]
-o [string]             operation on wpa_supplicant (start, stop, restart, cli)
-n			force normal mode (creates separate wpa_supplicant instances for every vdev)
-g [string]		global control interface path (specific to the default global mode) (optional)
-p [number]             pdev number
-i [number or range]    vdev number or range of numbers (0-2048)
-c [file path]          wpa_supplicant conf file path
-C [string]             wpa_cli command
-h                      show this message
```

### Examples:

Use the global wpa_supplicant (/tmp/global) to start STA mode operation in vdevs from sta1 to sta10 with /etc/wpa_supplicant.conf file
```
$ ./supplicant -o start -p 0 -i 1-10 -c /etc/wpa_supplicant.conf
```

Run commands while specifying the global wpa_supplicant ctrl interface path (expected default path is /tmp/global)
```
$ ./supplicant -g /root/global -o start -p 0 -i 1-10 -c /etc/wpa_supplicant.conf
```

Create individual wpa_supplicant instances to start STA mode operation in vdevs from sta1 to sta10 with /etc/wpa_supplicant.conf file
```
$ ./supplicant -n -o start -p 0 -i 1-10 -c /etc/wpa_supplicant.conf
```

Stop the wpa_supplicant instance running on vdev sta1
```
$ ./supplicant -n -o stop -p 0 -i 1
```

Use the global wpa_supplicant to restart the STA vdevs from sta3 to sta5 with /etc/wpa_supplicant.conf file
```
$ ./supplicant -o restart -p 0 -i 3-5 -c /etc/wpa_supplicant.conf
```

Run the wpa_cli command \"status\" on vdev sta2
```
$ ./supplicant -o cli -p 0 -i 2 -C status
```

Run the wpa_cli command \"get_network 0 ssid\" on vdev sta2
```
$ ./supplicant -o cli -p 0 -i 2 -C "get network 0 ssid"
```

## License & copyright

multi-sta is made available under the [MIT License](LICENSE)
