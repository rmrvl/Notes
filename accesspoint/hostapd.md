Documentation of main features of hostapd\

# Interface and drivers
```
interface=wlan0
driver=nl80211
```

# Wifi IEEE 802.11

## Setup bandwidth

Can be configured using `hw_mode`\
`hw_mode=b` should set the wifi 1\
`hw_mode=a` specify 5GHz bandwidth\
`hw_mode=g` specify 2.4GHz bandwidth\

## Wifi 6 - IEEE 802.11ac
To create a Wifi 6 AP use `hw_mode=a`\

`ieee80211ac=0` disable the Wifi 6 VHT (default)\
`ieee80211ac=1` enable it\

`vht_capab` can be used to configure VHT by appending possible flags\

example
```
vht_capab=[HT20][MAX-MPDU-7991][VHT160]
```
Check [HostAPD Documentation](https://github.com/latelee/hostapd/blob/master/hostapd/hostapd.conf)

## Wifi 5 - IEEE 802.11n

Can be used with `hw_mode=g` and `hw_mode=a`\

`ieee80211n=0` disable the wifi 5 HT (default)\
`ieee80211n=1` enable it\

`ht_capab` to configure HT by appending possible flags (same as vht\_capab)\

## IEEE 802.11d
`ieee80211d=0` disabled (default)\
`ieee80211d=1` enabled\

It is required to add the country code : `country_code=US`\

Add power constraint using `local_pwr_constraint=150` with value in 0..255\
Spectrum management `spectrum_mgmt_required=1` (default 0)


## Channels
`channel=0` Automatic channel selection (require the ACS support)\
`channel=1` Use wifi channel 1


## Activate Quality of Service
```
wmm_enabled=1
```

## SSID

controled by `ssid`
```
ssid=MyHotSpot
```

# Security

## Authentication Algorithm

Controlled by `auth_algs` instruction by settings the 2 bits\
`auth_algs=1` wpa\
`auth_algs=2` wep\
`auth_algs=3` both

## WPA

### Version

Controlled by `wpa` instruction by settings the 2 bits\
`wpa=1` version 1\
`wpa=2` version 2\
`wpa=3` both

### Passphrase

Access can be granted using passphrase or pre-shared-key
`wpa_pasphrase=passphrase` the passphrase for the wifi
`wpa_psk=70617373706872617365` the hexadecimal 256-bits secret
`wpa_psk_file=/etc/secret.wpa_psk` read the psk from file

### Accepted Key Management

Set the accepted key management using `WPA-PSK` and `WPA-EAP`\
Can also use their **SHA256** version using `WPA-PSK-SHA256` and `WPA-EAP-SHA256`
```
wpa_key_mgmt=WPA-PSK WPA-EAP
```

**WPA3** uses the SAE key management and can be activated with `wpa_key_mgmt=SAE`

### Cipher Suites

The different ciphersuite values can be set using `CCMP` or `TKIP`\
If using WPA1 : `wpa_pairwise = TKIP CCMP`
If using WPA2 : `rsn_pairwise=CCMP`
