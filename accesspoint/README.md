To implement an access point using wlan0 with a device connected on internet through eth0\

# Install hostapd

```
sudo apt install hostapd
sudo systemctl unmask hostapd
sudo systemctl enable hostapd
```

## In /etc/default/hostapd

`DAEMON_CONF="/etc/hostapd/hostapd.conf"`

## In /etc/hostapd/hostapd.conf

Create the file /etc/hostapd/hostapd.conf and add the following lines

```
interface=wlan0
driver=nl80211
hw_mode=g
channel=6
auth_algs=1
wpa=2
wpa_key_mgmt=WPA-PSK
wpa_pairwise=CCMP
rsn_pairwise=CCMP
ssid=<WIFI SSID>
wpa_psk=<hex passphrase if wpa_passphrase not used>
wpa_passphrase=<passphrase if wpa_psk not used>
```

# Install dnsmasq
```
sudo apt install dnsmasq
sudo systemctl enable dnsmasq
```

## In /etc/dnsmasq.conf
```
interface=wlan0
domain-needed
bogus-priv
filterwin2k
server=9.9.9.9
listen-address=0.0.0.0
no-hosts
dhcp-range=10.1.1.50,10.1.1.60,12h
dhcp-option=option:netmask,255.255.255.0
dhcp-option=option:router,10.1.1.1
dhcp-option=option:dns-server,10.1.1.1
```

DHCP server can be configured by interfaces `dhcp-range=wlan0,10.1.1.50,10.1.1.60,12h`

# Routing

```
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE\
sudo iptables -A FORWARD -i eth0 -o wlan0 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT\
sudo iptables -A FORWARD -i wlan0 -o eth0 -j ACCEPT
```

# Generate QRCode using qrencode

It is possible to generate a Connection QRCode into the terminal

```
sudo apt install qrencode
qrencode -t ANSI256UTF8 'WIFI:T:WPA;S:<WIFI SSID>;P:<Wifi Pass>;;'
```

