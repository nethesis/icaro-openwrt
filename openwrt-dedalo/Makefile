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

define Package/openwrt-dedalo/postinst
#!/bin/sh
if [ -z "$${IPKG_INSTROOT}" ]; then
uci batch <<EOF
$(Package/openwrt-dedalo/networks_config)
EOF
else
echo "#!/bin/sh" > $${IPKG_INSTROOT}/etc/uci-defaults/dedalo
echo uci batch "<<EOF" "$(Package/openwrt-dedalo/networks_config)""EOF" >> $${IPKG_INSTROOT}/etc/uci-defaults/dedalo
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

endef

$(eval $(call BuildPackage,openwrt-dedalo))
