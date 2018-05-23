include $(TOPDIR)/rules.mk

PKG_NAME:=dedalo
PKG_VERSION:=0.2.0
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/nethesis/icaro.git
PKG_SOURCE_VERSION:=v16

include $(INCLUDE_DIR)/package.mk

define Package/dedalo
  SECTION:=base
  CATEGORY:=Network
  TITLE:=Network Access Controller, runs on the firewall and intercepts all guest connections
  URL:=https://nethesis.github.io/icaro/
  PKGARCH:=all
endef

define Package/dedalo/description
Dedalo is the Network Access Controller, runs on the firewall and intercepts all guest connections.
It's a configuration helper for Coova-Chilli
endef

define Build/Compile
endef

define Package/dedalo/install
	$(INSTALL_DIR) $(1)/opt/icaro/dedalo

	$(INSTALL_BIN) $(PKG_BUILD_DIR)/dedalo/dedalo $(1)/opt/icaro/dedalo/dedalo
	$(INSTALL_CONF) $(PKG_BUILD_DIR)/dedalo/config $(1)/opt/icaro/dedalo/config
	
	$(INSTALL_DIR) $(1)/opt/icaro/dedalo/template
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/dedalo/template/engine $(1)/opt/icaro/dedalo/template/engine
	$(INSTALL_CONF) $(PKG_BUILD_DIR)/dedalo/template/chilli.conf.tpl $(1)/opt/icaro/dedalo/template/chilli.conf.tpl
	
	$(INSTALL_DIR) $(1)/opt/icaro/dedalo/walled_gardens
	$(INSTALL_CONF) $(PKG_BUILD_DIR)/dedalo/walled_gardens/facebook.conf $(1)/opt/icaro/dedalo/walled_gardens/facebook.conf
	$(INSTALL_CONF) $(PKG_BUILD_DIR)/dedalo/walled_gardens/linkedin.conf $(1)/opt/icaro/dedalo/walled_gardens/linkedin.conf
	$(INSTALL_CONF) $(PKG_BUILD_DIR)/dedalo/walled_gardens/instagram.conf $(1)/opt/icaro/dedalo/walled_gardens/instagram.conf
	
	$(INSTALL_DIR) $(1)/opt/icaro/dedalo/www
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/dedalo/www/temporary.chi $(1)/opt/icaro/dedalo/www/temporary.chi
	
	touch $(1)/opt/icaro/dedalo/local.conf
	touch $(1)/opt/icaro/dedalo/walled_gardens/local.conf
endef

$(eval $(call BuildPackage,dedalo))
