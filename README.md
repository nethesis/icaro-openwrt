# Openwrt port of Icaro HotSpot

Minimum HW requirements:

| RAM   | Flash |
|-------|-------|
| 32 MB |  8 MB |

Pre compiled architectures:

  * mips_24kc
  * mipsel_24kc
  * arm_cortex-a7_neon-vfpv4

List of compatible devices: https://openwrt.org/supported_devices

Tested OpenWrt releases:

 * OpenWrt 18.06.1
 * LEDE 17.01.5

Tested devices:

* [Asus RT-N14U](https://openwrt.org/toh/asus/rt-n14u)
* [GL.iNet GL-AR300M](https://www.gl-inet.com/products/gl-ar300m/)
* [GL.iNet GL-AR750](https://www.gl-inet.com/products/gl-ar750/)
* [Raspberry Pi 2](https://openwrt.org/toh/raspberry_pi_foundation/raspberry_pi)

## Pre-setup for devices with only one network interface (eg. Raspberry Pi 2)

As default configuration Openwrt will set the interface as `lan` with static ip address and dhcp server bound on it.
For make the installation process easy is recommended to delete the `lan` network and create a `wan` network in the interface:

1. Disable the firwall so you can easy connect to the device via the wan network:
```console
# /etc/init.d/firewall stop
```
2. Delete the `lan` network and create the `wan` network with dhcp client configuration
```console
# uci delete network.lan
# uci set network.wan=interface
# uci set network.wan.ifname=eth0
# uci set network.wan.proto=dhcp
# uci commit
# /etc/init.d/network restart
```

Install all the kernel modules for use an additional usb ethernet adapter:
```console
# opkg update
# opkg install $(opkg find kmod-usb-net* | cut -d ' ' -f 1)
```

## Installation

1. Enable https for opkg packages lists.
```shell
	# opkg update
	# opkg install libustream-mbedtls

```
2. Add Icaro repository for your architecture:

To find your architecture use `opkg print-architecture` command, e.g.:
```
root@GL-AR300M:~# opkg print-architecture
arch all 1
arch noarch 1
arch mips_24kc 10
```
The architecture in this case is *mips_24kc*, replace it with every occurrence of the term *ARCH*

```shell
	# echo "src/gz icaro  http://nethesis.github.io/icaro-openwrt_repo/ARCH/icaro" >> /etc/opkg/customfeeds.conf
```
3. Disable signature check commenting the line ``option check_signature 1`` in ``/etc/opkg.conf``

4. Update packages list and install `openwrt-dedalo`:
```shell
	# opkg update
	# opkg install openwrt-dedalo
```

## Network Configuration

We suggest to use the following network configuration:

1. WAN 

WAN must be connected to the router in DHCP or with a static IP address.Using a static IP address could be easier to manage if you need to access the OpenWrt device.
Once you configured the WAN go to firewall settings and add 2 rules to permit management access to your OpenWrt device.
Usually we have http protocol (port 80 TCP) and ssh protocol (port 22 TCP).

2. Hotspot NetWork

Add at least one physical interface (Wireless or LAN) to the hotspot network, so that hotspot can use it.
You can use either LAN interface or WIRELESS interface or both together.

**Important**
After having done it go to the specific interface settings and make sure the interface is only assigned to hotspot network (you need to unlink other networks, e.g. LAN from the physical interface).

3. Wireless Security

Go to Network-> Wireless -> Wireless Security and disable encription, hotspot tipically requires an open network without any authentication.


## First setup

**Set up network configuration BEFORE this steps**

1. Generate a UUID using:
 ```shell
	# uuidgen
 ```
 
 
2. Edit `/etc/config/dedalo` with your configurations settings

Configuration example:

```
config dedalo
	option disabled '0'
	option network '192.168.69.0/24'
        option splash_page 'http://icaro.mydomain.com/wings'
        option aaa_url 'https://icaro.mydomain.com/wax/aaa'
        option api_url 'https://icaro.mydomain.com/api'
	option hotspot_id '176'
	option unit_name 'hotelthesea.example.org'
	option unit_description 'MyHotelAtTheSea'
	option unit_uuid '161fre6d-8578-4247-b4a2-c40dced94bdd'
	option secret 'My$uperS3cret'
	option allow_origin '*'
```

Available options:

- `disabled`: Enable/disable dedalo service (default true)
- `network`: network for clients connected to Dedalo eg: `192.168.69.0/24`
- `splash_page`: Wings (capitve portal) URL hosted on your Icaro installation, eg: ``http://icaro.mydomain.com/wings``
- `aaa_url`:  Wax (Radius over HTTP) URL hosted on your Icaro installation, eg: ``https://icaro.mydomain.com/wax/aaa``
- `api_url`: Sun APIs URL hosted on your Icaro installation, eg: ``https://icaro.mydomain.com/api``
- `hotspot_id`:  the id of the Hotspot already present inside Icaro
- `unit_name`: hostname of local installation, eg: ``hotelthesea.example.org``
- `unit_description`: a descriptive name of local installation, eg: ``MyHotelAtTheSea``
- `unit_uuid`:  a unique unit idenifier, usually a UUID, eg ``161fre6d-8578-4247-b4a2-c40dced94bdd``
- `secret`: a shared secret between this unit and Icaro installation, eg: ``My$uperS3cret``

 
3. Reload dedalo:
 ```shell
	# /etc/init.d/dedalo reload
 ```
4. Register dedalo unit:
 ```shell
	# dedalo register -u <your reseller username> -p <your reseller password>
 ```
5. Restart dedalo:
 ```shell
	# dedalo restart
 ```
6. Some device need a physical restart for change to be apply correctly (some GL-iNet):
 ```shell
	# reboot
 ```

## Development

1. Follow steps on https://openwrt.org/docs/guide-developer/using_the_sdk

2. Add icaro feeds in `feeds.conf.default` file:
 ```
	src-git icaro https://github.com/nethesis/icaro-openwrt.git
 ```
or path to your local clone:
 ```
	src-link icaro /full/path/to/the/local/folder
 ```
3. After `./scripts/feeds update -a` install icaro related packages with:
 ```shell
	$ ./scripts/feeds install openwrt-dedalo
 ```
4. Open configuration menu with `make menuconfig` then select `openwt-dedalo` under Network menu.

5. Compile with `make`

6. The generated repostory with .ipk files are placed in the `bin/packages/<arch>/icaro` directory eg. `bin/packages/mipsel_24kc/icaro`
