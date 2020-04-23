# wifiscan.sh

wifiscan.sh is a shell script and awk based Linux `iw <devname> scan` parsing tool.
It converts the scanning results to a JSON array, so that the converted results can be easily queried and manipulated by tools like [jq](https://stedolan.github.io/jq/).

* Unicode SSID names are of course supported.
* Busybox compatible.

## Table of Contents

* [Usage](#usage)
* [Use case](#use-case)
    - [Query SSIDs](#query-ssids)
    - [Auto channel selection (ACS)](#auto-channel-selection-asc)
* [Credits](#credits)

## Usage

```
wifiscan <devname>
```

*Example*

```
wifiscan wlan0
```

*Result*

```
[
{"mac":"c2:4b:e6:1e:95:b3","ssid":"Angel 的 Chromecast","freq":"2412","sig":"-62.00 dBm","sig%":"63.3333","wpa":"n","wpa2":"y","wep":"n","tkip":"n","ccmp":"y"},
{"mac":"1c:49:7b:30:23:3f","ssid":"TAO","freq":"2412","sig":"-56.00 dBm","sig%":"73.3333","wpa":"n","wpa2":"y","wep":"n","tkip":"n","ccmp":"y"}
]
```

## Use case

### Query SSIDs

```
wifiscan wlan0 | jq -r '.[] | .ssid' | sort -u
```

*Result*

```
Angel 的 Chromecast
TAO
```

### Auto channel selection (ACS)

Getting the least used channel among all available ones.

```
auto_select_channel() {
  echo $(cat << EOF | sort | uniq -c | sort | while read -r line; do if [ "$(echo "$line" | awk '{print $2}')" -lt 2467 ]; then echo "$line" | awk '{print $2}'; break; fi; done
2412
2417
2422
2427
2432
2437
2442
2447
2452
2457
2462
$(wifiscan wlan0 | jq -r '.[] | .freq')
$(wifiscan wlan0 | jq -r '.[] | .freq')
$(wifiscan wlan0 | jq -r '.[] | .freq')
EOF
    )
}

auto_select_channel
```

*Result*

```
2422
```

## Credits

* https://gist.github.com/elecnix/182fa522da5dc7389975
