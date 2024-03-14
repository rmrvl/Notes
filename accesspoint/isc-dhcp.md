```
sudo apt install isc-dhcp-server\
sudo systemctl enable isc-dhcp-server
```

# Configure the dhcp server
In /etc/default/isc-dhcp-server\

DHCPDv4\_CONF=/etc/dhcp/dhcpd.conf\
DHCPDv6\_CONF=/etc/dhcp/dhcpd6.conf\

```
INTERFACESv4="eth0"\
INTERFACESv6=""
```
# In /etc/dhcp/dhcpd.conf

```
subnet 10.0.0.0 netmask 255.255.255.0 {\
	> default-lease-time 600;\
	+ max-lease-time 7200;\
	+ range 10.0.0.50 10.0.0.200;\
	+ option subnet-mask 255.255.255.0;\
	+ option broadcast-address 10.0.0.255;\
	+ option routers 10.0.0.1;\
	+ option domain-name-servers 8.8.8.8;\
	+ option domain-name "mydomain.org";\
	+ option ntp-servers 10.10.0.1;\
}
```
