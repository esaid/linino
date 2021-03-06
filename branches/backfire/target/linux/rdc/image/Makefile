#
# Copyright (C) 2006-2010 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/image.mk

define Image/Prepare
	$(CP) $(LINUX_DIR)/arch/x86/boot/bzImage $(KDIR)/bzImage
endef

define Image/Build/ar525w
	cp $(KDIR)/bzImage $(KDIR)/bzImage.tmp
	$(SCRIPT_DIR)/pad_image $(1) $(KDIR)/bzImage.tmp $(KDIR)/root.$(1) 32
	$(STAGING_DIR_HOST)/bin/airlink -b 1 $(KDIR)/bzImage.tmp $(KDIR)/root.$(1) $(BIN_DIR)/$(IMG_PREFIX)-$(1)-$(2).img
	$(STAGING_DIR_HOST)/bin/airlink -e -b 1 $(KDIR)/bzImage.tmp $(KDIR)/root.$(1) $(BIN_DIR)/$(IMG_PREFIX)-$(1)-$(2)-web.img
endef

define Image/Build/sitecom
	cp $(KDIR)/bzImage $(KDIR)/bzImage.tmp
	#32k config data + 20 bytes header + 2 bytes checksum after kernel image
	$(SCRIPT_DIR)/pad_image $(1) $(KDIR)/bzImage.tmp $(KDIR)/root.$(1) 32790
	$(TOPDIR)/target/linux/rdc/image/mkimg_sitecom.pl $(KDIR)/bzImage.tmp > $(KDIR)/tmp.img
	cat $(KDIR)/root.$(1) >> $(KDIR)/tmp.img
	cp $(KDIR)/tmp.img $(BIN_DIR)/$(IMG_PREFIX)-$(1)-$(2).img
	rm $(KDIR)/tmp.img $(KDIR)/bzImage.tmp
endef

define Image/Build/bifferboard
	$(TOPDIR)/target/linux/rdc/image/mkimg_bifferboard.py $(KDIR)/bzImage $(KDIR)/root.$(1) $(KDIR)/tmp.img
	$(CP) $(KDIR)/tmp.img $(BIN_DIR)/$(IMG_PREFIX)-$(1)-$(2).img
endef

define Image/Build/Initramfs
	$(CP) $(KDIR)/bzImage $(BIN_DIR)/$(IMG_PREFIX)-ramfs.bzImage
endef

define Image/Build
	$(CP) $(KDIR)/bzImage $(BIN_DIR)/$(IMG_PREFIX).bzImage
	$(call Image/Build/$(PROFILE),$(1),$(PROFILE),$(patsubst jffs2-%k,%,$(1)))
ifeq ($(CONFIG_TARGET_ROOTFS_INITRAMFS),y)
	$(call Image/Build/Initramfs)
endif
endef

$(eval $(call BuildImage))
