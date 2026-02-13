```
Usage: ./bsbf-client-openwrt-config-generator.sh --server-ipv4 <ADDR>
       --server-port <PORT> --uuid <UUID> [--tcp-in-udp-big-endian --no-luci
       --dongle-modem --quectel-modem --usb-adapters-and-android-tethering
       --ios-tethering --mikrotik-tools --diag-tools --perf-test]
```

Once the script is run, the package list and the uci-defaults script can be used
as below to request an image from OpenWrt's firmware-selector:

```
jq -Rs '
{
  defaults: .,
  packages: [
    "ethtool",
    "fping",
    "ip-full",
    "tc-full",
    "kmod-sched",
    "kmod-sched-bpf",
    "coreutils-base64",
    "xray-core",
    "kmod-nft-tproxy",
    "-luci"
  ],
  profile: "mikrotik_routerboard-m33g",
  target: "ramips/mt7621",
  version: "24.10.5"
}
' 99-bsbf-bonding |
curl -X POST https://sysupgrade.openwrt.org/api/v1/build \
	-H "Content-Type: application/json" \
	-d @-
```

Or supplied to imagebuilder with `files/etc/uci-defaults/99-bsbf-bonding`:

```
PACKAGES=$(./bsbf-client-openwrt-imagebuilder-config-generator.sh --server-ipv4 <ADDR> --server-port <PORT> --uuid <UUID>) && \
install -D 99-bsbf-bonding ../openwrt-imagebuilders/openwrt-imagebuilder-24.10.5-ramips-mt7621.Linux-x86_64/files/etc/uci-defaults/99-bsbf-bonding && \
cd ../openwrt-imagebuilders/openwrt-imagebuilder-24.10.5-ramips-mt7621.Linux-x86_64 && \
make image \
PROFILE="mikrotik_routerboard-m33g" \
PACKAGES="$PACKAGES" \
FILES="files"
```
