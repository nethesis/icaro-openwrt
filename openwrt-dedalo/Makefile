include $(TOPDIR)/rules.mk

PKG_NAME:=openwrt-dedalo
PKG_VERSION:=0.0.1
PKG_RELEASE:=1

include $(INCLUDE_DIR)/package.mk

define Package/openwrt-dedalo
  SECTION:=base
  CATEGORY:=Network
  TITLE:=Dedalo integration for Openwrt
  URL:=https://nethesis.github.io/icaro/
  PKGARCH:=all
  DEPENDS:=+dedalo +uuidgen
endef

define Package/openwrt-dedalo/description
Dedalo capitve portal based on CoovaChilli
endef

define Build/Compile
endef

define Package/openwrt-dedalo/conffiles
/etc/config/dedalo
endef


define Package/openwrt-dedalo/networks_config

set network.hotspot=interface
set network.hotspot.proto='none'
set network.hotspot.type=bridge
set network.hotspot.ipv6=0

set network.dedalo=interface
set network.dedalo.proto='none'
set network.dedalo.ifname='tun-dedalo'
set network.dedalo.ipv6=0

commit

endef

define Package/openwrt-dedalo/firewall_config

set firewall.hotspot=zone
set firewall.hotspot.name='hotspot'
set firewall.hotspot.network='hotspot'
set firewall.hotspot.mtu_fix=1
set firewall.hotspot.forward='DROP'
set firewall.hotspot.input='DROP'
set firewall.hotspot.output='ACCEPT'

set firewall.dedalo=zone
set firewall.dedalo.name='dedalo'
set firewall.dedalo.network='dedalo'
set firewall.dedalo.mtu_fix=1
set firewall.dedalo.forward='DROP'
set firewall.dedalo.input='DROP'
set firewall.dedalo.output='ACCEPT'

set firewall.hs_user=forwarding
set firewall.hs_user.src='dedalo'
set firewall.hs_user.dest='wan'

set firewall.hs_uamport=rule
set firewall.hs_uamport.name='hs_uamport'
set firewall.hs_uamport.src='dedalo'
set firewall.hs_uamport.proto='tcp'
set firewall.hs_uamport.dest_port='3990'
set firewall.hs_uamport.target='ACCEPT'

set firewall.hs_dhcp_broadcast=rule
set firewall.hs_dhcp_broadcast.name='hs_dhcp_broadcast'
set firewall.hs_dhcp_broadcast.family='ipv4'
set firewall.hs_dhcp_broadcast.src='dedalo'
set firewall.hs_dhcp_broadcast.proto='udp'
set firewall.hs_dhcp_broadcast.dest_ip='255.255.255.255'
set firewall.hs_dhcp_broadcast.dest_port='67:68'
set firewall.hs_dhcp_broadcast.target='ACCEPT'

set firewall.hs_dhcp=rule
set firewall.hs_dhcp.name='hs_dhcp'
set firewall.hs_dhcp.src='dedalo'
set firewall.hs_dhcp.proto='udp'
set firewall.hs_dhcp.dest_port='67:68'
set firewall.hs_dhcp.target='ACCEPT'

set firewall.hs_dns=rule
set firewall.hs_dns.name='hs_dns'
set firewall.hs_dns.src='dedalo'
set firewall.hs_dns.proto='udp'
set firewall.hs_dns.dest_port='53'
set firewall.hs_dns.target='ACCEPT'

set firewall.hs_icmp=rule
set firewall.hs_icmp.name='hs_icmp'
set firewall.hs_icmp.src='dedalo'
set firewall.hs_icmp.proto='icmp'
set firewall.hs_icmp.target='ACCEPT'

set firewall.host_include=include
set firewall.host_include.path='/etc/firewall.dedalo'

commit

endef

define Package/openwrt-dedalo/postinst
#!/bin/sh
if [ -z "$${IPKG_INSTROOT}" ]; then
uci batch <<EOF
$(Package/openwrt-dedalo/networks_config)
EOF
uci batch <<EOF
$(Package/openwrt-dedalo/firewall_config)
EOF
else
echo "#!/bin/sh" > $${IPKG_INSTROOT}/etc/uci-defaults/dedalo
echo uci batch "<<EOF" "$(Package/openwrt-dedalo/networks_config)""EOF" >> $${IPKG_INSTROOT}/etc/uci-defaults/dedalo
echo uci batch "<<EOF" "$(Package/openwrt-dedalo/firewall_config)""EOF" >> $${IPKG_INSTROOT}/etc/uci-defaults/dedalo
echo "exit 0" >> $${IPKG_INSTROOT}/etc/uci-defaults/dedalo
set +x /etc/uci-defaults/dedalo
fi
exit 0
endef

define Package/openwrt-dedalo/install
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) files/dedalo.init $(1)/etc/init.d/dedalo
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_DATA) files/dedalo.config $(1)/etc/config/dedalo
	$(INSTALL_DIR) $(1)/etc/
	$(INSTALL_DATA) files/firewall.dedalo $(1)/etc/firewall.dedalo
endef

$(eval $(call BuildPackage,openwrt-dedalo))
