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

define Package/openwrt-dedalo/install
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) files/dedalo.init $(1)/etc/init.d/dedalo
endef

$(eval $(call BuildPackage,openwrt-dedalo))
