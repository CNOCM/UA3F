include $(TOPDIR)/rules.mk

PKG_NAME:=UA3F
PKG_VERSION:=0.7.3
PKG_RELEASE:=1

# PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
# PKG_SOURCE_URL:=https://codeload.github.com/SunBK201/UA3F/tar.gz/v$(PKG_VERSION)?
# PKG_HASH:=660a89fbde16ec769f256e83dc9479bf3fb2309e0c26fe88b3e4e55fbf6fbf8d

PKG_MAINTAINER:=SunBK201 <sunbk201gm@gmail.com>
PKG_LICENSE:=GPL-3.0-only
PKG_LICENSE_FILES:=LICENSE

PKG_BUILD_DEPENDS:=golang/host
PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=no-mips16

GO_PKG:=github.com/sunbk201/ua3f
GO_PKG_LDFLAGS_X:= main.version=$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk
include $(TOPDIR)/feeds/packages/lang/golang/golang-package.mk

define Package/ua3f
	SECTION:=net
	CATEGORY:=Network
	SUBMENU:=Web Servers/Proxies
	TITLE:=A SOCKS5 Server for Change User-Agent
	URL:=https://github.com/SunBK201/UA3F
	DEPENDS:=$(GO_ARCH_DEPENDS) +luci-compat
endef

define Package/ua3f/description
	Implementation of the next generation of HTTP User-Agent modification methodology.
endef

define Build/Prepare
	$(CP) ../src/* $(PKG_BUILD_DIR)
endef

define Package/ua3f/conffiles
/etc/config/ua3f
endef

define Package/ua3f/install
	$(call GoPackage/Package/Install/Bin,$(PKG_INSTALL_DIR))

	$(INSTALL_DIR) $(1)/usr/bin/
	# $(INSTALL_BIN) $(GO_PKG_BUILD_BIN_DIR)/ua3f $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/ua3f $(1)/usr/bin/
	$(INSTALL_DIR) $(1)/etc/init.d/
	$(INSTALL_BIN) ./files/ua3f.init $(1)/etc/init.d/ua3f
	$(INSTALL_DIR) $(1)/etc/config/
	$(INSTALL_CONF) ./files/ua3f.uci $(1)/etc/config/ua3f
	$(INSTALL_DIR) $(1)/usr/lib/lua/luci/model/cbi/
	$(INSTALL_CONF) ./files/luci/cbi.lua $(1)/usr/lib/lua/luci/model/cbi/ua3f.lua
	$(INSTALL_DIR) $(1)/usr/lib/lua/luci/controller/
	$(INSTALL_CONF) ./files/luci/controller.lua $(1)/usr/lib/lua/luci/controller/ua3f.lua
	
endef

$(eval $(call GoBinPackage,ua3f))
$(eval $(call BuildPackage,ua3f))
