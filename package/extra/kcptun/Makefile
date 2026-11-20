include $(TOPDIR)/rules.mk

PKG_NAME:=kcptun
PKG_VERSION:=20261113
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/xtaci/kcptun/tar.gz/v${PKG_VERSION}?
PKG_HASH:=295c234b2ff5f97cb0350adaa7ef67761aa293ff0d16d8e3089ea3144199ff54

PKG_MAINTAINER:=Dengfeng Liu <liudf0716@gmail.com>, Chao Liu <expiron18@gmail.com>
PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE.md

PKG_BUILD_PARALLEL:=1
PKG_USE_MIPS16:=0

# 本包在“核心包 CI”（.github/workflows/packages.yml，复用 openwrt/actions-shared-workflows）
# 中也会被解析，而该 CI 不安装 feeds，故 feeds/packages 里的 golang-package.mk 可能不存在。
# 无 golang feed 时只注册 kcptun-config，Go 二进制包交给带 feeds 的固件构建（build-*.yml）。
KCPTUN_GOLANG_MK:=$(TOPDIR)/feeds/packages/lang/golang/golang-package.mk

ifneq ($(wildcard $(KCPTUN_GOLANG_MK)),)
PKG_BUILD_DEPENDS:=golang/host
KCPTUN_HAVE_GOLANG:=1
GO_PKG:=github.com/xtaci/kcptun
GO_PKG_LDFLAGS_X:=main.VERSION=$(PKG_VERSION)
endif

include $(INCLUDE_DIR)/package.mk

# 无 golang feed 时（核心包 CI），kcptun-config 走默认 Build/Compile 会在
# Go 源码目录里执行 make（该目录没有 Makefile）而直接失败，这里改为空操作。
# 有 golang feed 时 golang-package.mk 会全局覆盖 Build/Compile，不受影响。
ifneq ($(KCPTUN_HAVE_GOLANG),1)
define Build/Compile
	:
endef
endif

ifneq ($(KCPTUN_HAVE_GOLANG),1)
GO_ARCH_DEPENDS:=
else
include $(KCPTUN_GOLANG_MK)
endif

define Package/kcptun-config
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Web Servers/Proxies
  TITLE:=Kcptun Config Scripts
  URL:=https://github.com/xtaci/kcptun
  DEPENDS:=$(GO_ARCH_DEPENDS)
endef

define Package/kcptun-config/conffiles
/etc/config/kcptun
endef

define Package/kcptun-config/install
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) ./files/kcptun.config $(1)/etc/config/kcptun
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/kcptun.init $(1)/etc/init.d/kcptun
endef

define Package/kcptun/Default
  define Package/kcptun-$(1)
    SECTION:=net
    CATEGORY:=Network
    SUBMENU:=Web Servers/Proxies
    TITLE:=KCP-based Secure Tunnel $(1)
    URL:=https://github.com/xtaci/kcptun
    DEPENDS:=+kcptun-config
  endef

  define Package/kcptun-$(1)/description
    kcptun is a Stable & Secure Tunnel Based On KCP with N:M Multiplexing.
This package only contains kcptun $(1).
  endef

  define Package/kcptun-$(1)/install
		$$(call GoPackage/Package/Install/Bin,$$(PKG_INSTALL_DIR))

		$$(INSTALL_DIR) $$(1)/usr/bin
		$$(INSTALL_BIN) $$(PKG_INSTALL_DIR)/usr/bin/$(1) $$(1)/usr/bin/kcptun-$(1)
  endef
endef

$(eval $(call BuildPackage,kcptun-config))

ifeq ($(KCPTUN_HAVE_GOLANG),1)
KCPTUN_COMPONENTS:=server client
$(foreach component,$(KCPTUN_COMPONENTS), \
  $(eval $(call Package/kcptun/Default,$(component))) \
  $(eval $(call GoBinPackage,kcptun-$(component))) \
  $(eval $(call BuildPackage,kcptun-$(component))) \
)
endif

