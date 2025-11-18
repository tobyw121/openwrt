include $(TOPDIR)/rules.mk

PKG_NAME:=rt3662-modules
PKG_VERSION:=2.4.0.9.10
PKG_RELEASE:=1

PKG_LICENSE:=Proprietary
PKG_MAINTAINER:=OpenWrt Community

include $(INCLUDE_DIR)/kernel.mk
include $(INCLUDE_DIR)/package.mk

PKG_BUILD_DIR:=$(KERNEL_BUILD_DIR)/$(PKG_NAME)-$(PKG_VERSION)

RT3662_SRC_DIR:=$(CURDIR)/../rt3662-modules-$(PKG_VERSION)
RT3662_VARIANTS:=pci pcie mii usb
newline := \

uppercase = $(shell echo $(1) | tr '[:lower:]' '[:upper:]')

define Build/Prepare
	rm -rf $(PKG_BUILD_DIR)
	mkdir -p $(PKG_BUILD_DIR)
	for v in $(RT3662_VARIANTS); do \
	rm -rf $(PKG_BUILD_DIR)/build-$$$$v; \
	mkdir -p $(PKG_BUILD_DIR)/build-$$$$v; \
	$(CP) $(RT3662_SRC_DIR)/. $(PKG_BUILD_DIR)/build-$$$$v/; \
	done
endef

define Build/Compile/variant
	$(MAKE) -C $(PKG_BUILD_DIR)/build-$(1) \
	LINUX_SRC="$(LINUX_DIR)" \
	CROSS_COMPILE="$(TARGET_CROSS)" \
	ARCH="$(LINUX_KARCH)" \
	CONFIG_CHIP_NAME=3662 \
	CONFIG_INF_TYPE=$(2) \
	CONFIG_EXTRA_CFLAG=y \
	CONFIG_RALINK_SRC=y \
	PLATFORM=OPENWRT
	$(CP) $(PKG_BUILD_DIR)/build-$(1)/rt3662_iNIC.$(LINUX_KMOD_SUFFIX) \
	$(PKG_BUILD_DIR)/rt3662_iNIC_$(1).$(LINUX_KMOD_SUFFIX)
endef

define Build/Compile
	$(KERNEL_MAKE) olddefconfig modules_prepare
	$(foreach v,$(RT3662_VARIANTS), \
		$(call Build/Compile/variant,$(v),$(call uppercase,$(v)))$(newline))
endef

define KernelPackage/rt3662-template
  SUBMENU:=Wireless Drivers
  TITLE:=Ralink RT3662 iNIC ($(1)) support
  DEPENDS:=@TARGET_ramips_rt3883
  FILES:=$(PKG_BUILD_DIR)/rt3662_iNIC_$(2).$(LINUX_KMOD_SUFFIX)
  AUTOLOAD:=$(call AutoProbe,rt3662_iNIC)
endef

define KernelPackage/rt3662-pci
  $(call KernelPackage/rt3662-template,PCI,pci)
endef

define KernelPackage/rt3662-pcie
  $(call KernelPackage/rt3662-template,PCIE,pcie)
endef

define KernelPackage/rt3662-mii
  $(call KernelPackage/rt3662-template,MII,mii)
endef

define KernelPackage/rt3662-usb
  $(call KernelPackage/rt3662-template,USB,usb)
endef

$(eval $(call KernelPackage,rt3662-pci))
$(eval $(call KernelPackage,rt3662-pcie))
$(eval $(call KernelPackage,rt3662-mii))
$(eval $(call KernelPackage,rt3662-usb))
