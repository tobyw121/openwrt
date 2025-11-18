include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=rt3662-inic
PKG_RELEASE:=1
PKG_LICENSE:=Proprietary
PKG_MAINTAINER:=OpenWrt Community

PKG_BUILD_DIR:=$(KERNEL_BUILD_DIR)/$(PKG_NAME)

FIRMWARE_ARCHIVE:=rt3662-modules-2.4.0.9.10.zip
FIRMWARE_ARCHIVE_DIR:=rt3662-modules-2.4.0.9.10
FIRMWARE_ARCHIVE_URL:=https://www.how-toby.de/index.php/s/Bmn37GCB5SZM3GR/download
FIRMWARE_ARCHIVE_HASH:=fb6df7ce232f606c864c3b2acf42e48153a6c01f0518719accc3de975538bcec

define Download/rt3662-inic-firmware
  FILE:=$(FIRMWARE_ARCHIVE)
  URL:=$(FIRMWARE_ARCHIVE_URL)
  HASH:=$(FIRMWARE_ARCHIVE_HASH)
endef
$(eval $(call Download,rt3662-inic-firmware))

PKG_CONFIG_DEPENDS:= \
CONFIG_RT3662_INIC_INF_USB \
CONFIG_RT3662_INIC_INF_PCI \
CONFIG_RT3662_INIC_INF_PCIE \
CONFIG_RT3662_INIC_INF_MII \
CONFIG_RT3662_INIC_AGGREGATION \
CONFIG_RT3662_INIC_NEW_MBSS \
CONFIG_RT3662_INIC_CONCURRENT

include $(INCLUDE_DIR)/package.mk

define KernelPackage/rt3662-inic
  SECTION:=kernel
  CATEGORY:=Kernel modules
  SUBMENU:=Wireless Drivers
  TITLE:=Ralink RT3662 iNIC driver
  DEPENDS:=@TARGET_ramips
  FILES:=$(PKG_BUILD_DIR)/rt3662_iNIC.ko
  AUTOLOAD:=$(call AutoProbe,rt3662_iNIC)
endef

define KernelPackage/rt3662-inic/description
  Proprietary iNIC driver for the Ralink RT3662/RT3883 wireless module.
endef

define KernelPackage/rt3662-inic/config
source "$(SOURCE)/Config.in"
endef

INIC_INF_TYPE:=USB
ifeq ($(CONFIG_RT3662_INIC_INF_PCI),y)
INIC_INF_TYPE:=PCI
endif
ifeq ($(CONFIG_RT3662_INIC_INF_PCIE),y)
INIC_INF_TYPE:=PCIE
endif
ifeq ($(CONFIG_RT3662_INIC_INF_MII),y)
INIC_INF_TYPE:=MII
endif

INIC_FEATURES:=
ifeq ($(CONFIG_RT3662_INIC_AGGREGATION),y)
INIC_FEATURES+= Aggregation_Enable=y
endif
ifeq ($(CONFIG_RT3662_INIC_NEW_MBSS),y)
INIC_FEATURES+= CONFIG_NEW_MBSS_SUPPORT=y
endif
ifeq ($(CONFIG_RT3662_INIC_CONCURRENT),y)
INIC_FEATURES+= CONFIG_CONCURRENT_INIC_SUPPORT=y
endif

MAKE_FLAGS+= \
CONFIG_CHIP_NAME=3662 \
CONFIG_INF_TYPE=$(INIC_INF_TYPE) \
CONFIG_EXTRA_CFLAG=y \
CONFIG_RALINK_SRC=y \
RALINK_SRC=$(PKG_BUILD_DIR) \
KERNELDIR=$(LINUX_DIR) \
$(INIC_FEATURES)

define Build/Prepare
	mkdir -p $(PKG_BUILD_DIR)
	rsync -a $(CURDIR)/src/ $(PKG_BUILD_DIR)/
	$(INSTALL_DIR) $(PKG_BUILD_DIR)/firmware/usb $(PKG_BUILD_DIR)/firmware/mii
	unzip -p $(DL_DIR)/$(FIRMWARE_ARCHIVE) $(FIRMWARE_ARCHIVE_DIR)/firmware/usb/iNIC_ap.bin > $(PKG_BUILD_DIR)/firmware/usb/iNIC_ap.bin
	unzip -p $(DL_DIR)/$(FIRMWARE_ARCHIVE) $(FIRMWARE_ARCHIVE_DIR)/firmware/usb/iNIC_sta.bin > $(PKG_BUILD_DIR)/firmware/usb/iNIC_sta.bin
	unzip -p $(DL_DIR)/$(FIRMWARE_ARCHIVE) $(FIRMWARE_ARCHIVE_DIR)/firmware/mii/iNIC_sta.bin > $(PKG_BUILD_DIR)/firmware/mii/iNIC_sta.bin
endef

define Build/Compile
	$(KERNEL_MAKE) M=$(PKG_BUILD_DIR) modules $(MAKE_FLAGS)
endef

$(eval $(call KernelPackage,rt3662-inic))
