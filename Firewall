# Iptables

sudo iptables -P INPUT DROP
sudo iptables -P OUTPUT DROP
sudo iptables -P FORWARD DROP

## Allow localhost
iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A INPUT -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT

## Allow DNS
iptables -t filter -A OUTPUT -p udp --dport 53 -j ACCEPT
iptables -t filter -A INPUT -p udp --sport 53 -j ACCEPT

iptables -t filter -A OUTPUT -p tcp --dport 53 -j ACCEPT
iptables -t filter -A INPUT -p tcp --sport 53 -j ACCEPT

## ICMP
iptables -t filter -A OUTPUT -p icmp -j ACCEPT
iptables -t filter -A INPUT -p icmp -j ACCEPT

## DHCP
iptables  -I INPUT -i eth1 -p udp --dport 67:68 --sport 67:68 -j ACCEPT
iptables  -I OUTPUT -i eth1 -p udp --dport 67:68 -j ACCEPT

## Connections Established
iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -m state --state RELATED,ESTABLISHED -j ACCEPT

## Allow SSH/HTTP
iptables -t filter -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -t filter -A OUTPUT -p tcp --dport 22 -j ACCEPT
iptables -t filter -A OUTPUT -p tcp --dport 80 -j ACCEPT
iptables -t filter -A INPUT -p tcp --dport 80 -j ACCEPT

## Allow access to a service
sudo iptables -A INPUT -p tcp --dport 22 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
sudo iptables -A OUTPUT -p tcp --sport 22 -m conntrack --ctstate ESTABLISHED -j ACCEPT

### From specific IP
sudo iptables -A INPUT -p tcp -s 15.15.15.0/24 --dport 22 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
sudo iptables -A OUTPUT -p tcp --sport 22 -m conntrack --ctstate ESTABLISHED -j ACCEPT
