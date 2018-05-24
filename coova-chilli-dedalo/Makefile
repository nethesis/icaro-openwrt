#
# Copyright (C) 2007-2018 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=coova-chilli-dedalo
PKG_VERSION:=1.3.0+20141128
PKG_MAINTAINER:=Matteo Valentini <matteo.valentini@nethesis.it>
PKG_LICENSE:=GPL-2.0+
PKG_LICENSE_FILES:=COPYING
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=git://github.com/coova/coova-chilli
PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
PKG_SOURCE_VERSION:=b93de20a288c01c2ba28e96e31ad6da01627f45f
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_MIRROR_HASH:=89c9b313881c658a0f6b91329a78bb1a0151878b19bc99b315976081c6355557

PKG_INSTALL:=1

PKG_BUILD_DEPENDS:=libcurl
PKG_CONFIG_DEPENDS := \
  COOVACHILLIDEDALO_MINIPORTAL \
  COOVACHILLIDEDALO_REDIR \
  COOVACHILLIDEDALO_USERAGENT \
  COOVACHILLIDEDALO_DNSLOG \
  COOVACHILLIDEDALO_UAMDOMAINFILE \
  COOVACHILLIDEDALO_LARGELIMITS \
  COOVACHILLIDEDALO_NOSSL \
  COOVACHILLIDEDALO_CYASSL \
  COOVACHILLIDEDALO_OPENSSL

include $(INCLUDE_DIR)/package.mk

define Package/coova-chilli-dedalo
  SUBMENU:=Captive Portals
  SECTION:=net
  CATEGORY:=Network
  DEPENDS:=+kmod-tun +librt +COOVACHILLIDEDALO_CYASSL:libcyassl +COOVACHILLIDEDALO_OPENSSL:libopenssl +libcurl +zlib
  TITLE:=Icaro fork of Coova Chilli
  URL:=http://www.coova.org/CoovaChilli
  MENU:=1
endef

define Package/coova-chilli-dedalo/description
	CoovaChilli is an open source access controller for wireless LAN
	access points and is based on ChilliSpot. It is used for authenticating
	users of a wireless (or wired) LAN. It supports web based login (UAM)
	which is today's standard for public HotSpots and it supports Wireless
	Protected Access (WPA) which is the standard of the future.
	Authentication,	authorization and accounting (AAA) is handled by your
	favorite radius server.
endef

define Package/coova-chilli-dedalo/config
  source "$(SOURCE)/Config.in"
endef

DISABLE_NLS=

TARGET_CFLAGS += $(FPIC)

CONFIGURE_VARS += \
       ARCH="$(LINUX_KARCH)" \
       KERNEL_DIR="$(LINUX_DIR)"

MAKE_FLAGS += \
       ARCH="$(LINUX_KARCH)" \
       KERNEL_DIR="$(LINUX_DIR)"

MAKE_INSTALL_FLAGS += \
       ARCH="$(LINUX_KARCH)" \
       KERNEL_DIR="$(LINUX_DIR)" \
       INSTALL_MOD_PATH="$(PKG_INSTALL_DIR)"

define Build/Prepare
$(call Build/Prepare/Default)
	( cd $(PKG_BUILD_DIR) ; \
		[ -f ./configure ] || { \
			./bootstrap ; \
		} \
	)
endef

define Build/Configure
	$(call Build/Configure/Default, \
	$(if $(CONFIG_COOVACHILLIDEDALO_PROXY),--enable,--disable)-chilliproxy \
	$(if $(CONFIG_COOVACHILLIDEDALO_REDIR),--enable,--disable)-chilliredir \
	$(if $(CONFIG_COOVACHILLIDEDALO_DNSLOG),--enable,--disable)-dnslog \
	$(if $(CONFIG_COOVACHILLIDEDALO_MINIPORTAL),--enable,--disable)-miniportal \
	$(if $(CONFIG_COOVACHILLIDEDALO_USERAGENT),--enable,--disable)-useragent \
	$(if $(CONFIG_COOVACHILLIDEDALO_LARGELIMITS),--enable,--disable)-largelimits \
	$(if $(CONFIG_COOVACHILLIDEDALO_UAMDOMAINFILE),--enable,--disable)-uamdomainfile \
	$(if $(CONFIG_COOVACHILLIDEDALO_CYASSL),--with,--without)-cyassl \
	$(if $(CONFIG_COOVACHILLIDEDALO_OPENSSL),--with,--without)-openssl \
	--with-curl="$(STAGING_DIR)/usr" \
	)
endef

define Package/coova-chilli-dedalo/conffiles
/etc/config/chilli
endef

define Package/coova-chilli-dedalo/install
	$(INSTALL_DIR) $(1)/etc
	$(INSTALL_CONF) $(PKG_INSTALL_DIR)/etc/chilli.conf $(1)/etc/
	$(INSTALL_DIR) $(1)/etc/chilli
	$(CP) $(PKG_INSTALL_DIR)/etc/chilli/* $(1)/etc/chilli/
	$(INSTALL_DIR) $(1)/etc/hotplug.d/iface
	$(INSTALL_DATA) ./files/chilli.hotplug $(1)/etc/hotplug.d/iface/30-chilli
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/sbin/chilli* $(1)/usr/sbin/
	$(INSTALL_DIR) $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/lib*.so.* $(1)/usr/lib/
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) files/chilli.init $(1)/etc/init.d/chilli
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_DATA) files/chilli.config $(1)/etc/config/chilli
	$(INSTALL_DIR) $(1)/lib/firewall
	$(CP) files/chilli.firewall $(1)/lib/firewall/chilli.sh
endef

$(eval $(call BuildPackage,coova-chilli-dedalo))
