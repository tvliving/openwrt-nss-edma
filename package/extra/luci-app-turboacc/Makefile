# SPDX-Identifier-License: GPL-3.0-only
#
# Copyright (C) 2024 Lean <coolsnowwolf@gmail.com>
# Copyright (C) 2019-2022 ImmortalWrt.org

include $(TOPDIR)/rules.mk

PKG_NAME:=luci-app-turboacc

PKG_CONFIG_DEPENDS:= \
	CONFIG_PACKAGE_TURBOACC_INCLUDE_NO_FASTPATH \
	CONFIG_PACKAGE_TURBOACC_INCLUDE_FLOW_OFFLOADING \
	CONFIG_PACKAGE_TURBOACC_INCLUDE_FAST_CLASSIFIER \
	CONFIG_PACKAGE_TURBOACC_INCLUDE_SHORTCUT_FE_CM \
	CONFIG_PACKAGE_TURBOACC_INCLUDE_BBR_CCA

PKG_LICENSE:=GPL-3.0-only
PKG_MAINTAINER:=Tianling Shen <cnsztl@immortalwrt.org>

LUCI_TITLE:=LuCI support for FastPath (FW3 or FW4)
LUCI_DEPENDS:= \
	+PACKAGE_TURBOACC_INCLUDE_FLOW_OFFLOADING:kmod-nft-offload \
	+PACKAGE_TURBOACC_INCLUDE_BBR_CCA:kmod-tcp-bbr
LUCI_PKGARCH:=all

define Package/luci-app-turboacc/config
  choice
    prompt "FastPath Engine"
    default PACKAGE_TURBOACC_INCLUDE_NO_FASTPATH

    config PACKAGE_TURBOACC_INCLUDE_NO_FASTPATH
    bool "Use NSS ECM"

    config PACKAGE_TURBOACC_INCLUDE_FLOW_OFFLOADING
    bool "Use flow offloading"
  endchoice

  config PACKAGE_TURBOACC_INCLUDE_BBR_CCA
    bool "Enable BBR CCA"
    default y
endef

include $(TOPDIR)/feeds/luci/luci.mk

# call BuildPackage - OpenWrt buildroot signature
