#Wifi Access Point

To implement an access point using wla0 with a device connected on internet through eth0

## Install hostapd
sudo apt install hostapd
sudo systemctl unmask hostapd
sudo systemctl enable hostapd

## In /etc/default/hostapd

DAEMON_CONF="/etc/hostapd/hostapd.conf"

## In /etc/hostapd/hostapd.conf

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
  
## Install isc-dhcp-server

sudo apt install isc-dhcp-server
sudo systemctl enable isc-dhcp-server

## In /etc/default/isc-dhcp-server
#DHCPDv4_CONF=/etc/dhcp/dhcpd.conf
#DHCPDv6_CONF=/etc/dhcp/dhcpd6.conf

INTERFACESv4="wlan0"
INTERFACESv6=""

## In /etc/dhcp/dhcpd.conf


subnet 10.10.0.0 netmask 255.255.255.0 {
	default-lease-time 600;
	max-lease-time 7200;
	range 10.10.0.50 10.10.0.200;
	option subnet-mask 255.255.255.0;
	option broadcast-address 10.10.0.255;
	option routers 10.10.0.1;
	option domain-name-servers 8.8.8.8;
	option domain-name "house.home";
	option ntp-servers 10.10.0.1;
}

## Routing

sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
sudo iptables -A FORWARD -i eth0 -o wlan0 -m state --state RELATED,ESTABLISHED -j ACCEPT
sudo iptables -A FORWARD -i wlan0 -o eth0 -j ACCEPT
