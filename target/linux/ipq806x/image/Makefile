# Copyright (c) 2014 The Linux Foundation. All rights reserved.
#
include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/image.mk

UBIFS_OPTS = -m 2048 -e 124KiB -c 4096 -U -F
UBINIZE_OPTS = -m 2048 -p 128KiB

E2SIZE=$(shell echo $$(($(CONFIG_TARGET_ROOTFS_PARTSIZE)*1024)))

define Image/BuildKernel/zImage
	cat $(KDIR)/zImage $(LINUX_DIR)/arch/arm/boot/dts/$(1).dtb > $(KDIR)/zImage-$(1)
	$(CP) $(KDIR)/zImage-$(1) $(BIN_DIR)/$(IMG_PREFIX)-$(1)-zImage
ifneq ($(CONFIG_TARGET_ROOTFS_INITRAMFS),)
	cat $(KDIR)/zImage-initramfs $(LINUX_DIR)/arch/arm/boot/dts/$(1).dtb > $(KDIR)/zImage-initramfs-$(1)
	$(CP) $(KDIR)/zImage-initramfs-$(1) $(BIN_DIR)/$(IMG_PREFIX)-$(1)-zImage-initramfs
endif
endef

define Image/BuildKernel/FIT
	gzip -9n -c $(KDIR)/Image > $(KDIR)/Image.gz
	$(call CompressLzma,$(KDIR)/Image,$(KDIR)/Image.gz)
	$(call Image/BuildKernel/MkFIT,$(1), $(KDIR)/Image.gz, $(LINUX_DIR)/arch/arm/boot/dts/$(1).dtb,gzip,0x42208000,0x42208000)
	$(CP) $(KDIR)/fit-$(1).itb $(BIN_DIR)/$(IMG_PREFIX)-$(1)-fit-uImage.itb
ifneq ($(CONFIG_TARGET_ROOTFS_INITRAMFS),)
	$(call Image/BuildKernel/MkFIT,$(1), $(KDIR)/Image-initramfs, $(LINUX_DIR)/arch/arm/boot/dts/$(1).dtb, none,0x42208000,0x42208000)
	$(CP) $(KDIR)/fit-$(1).itb $(BIN_DIR)/$(IMG_PREFIX)-$(1)-fit-uImage-initramfs.itb
endif
endef

define Image/Prepare
	$(CP) $(LINUX_DIR)/vmlinux $(KDIR)/$(IMG_PREFIX)-vmlinux.elf
endef

define Image/BuildKernel
	$(CP) $(KDIR)/$(IMG_PREFIX)-vmlinux.elf $(BIN_DIR)
	$(call Image/BuildKernel/FIT,qcom-ipq8064-ap148)
	$(call Image/BuildKernel/zImage,qcom-ipq8064-ap148)
	$(call Image/BuildKernel/FIT,qcom-ipq8064-db149)
endef

define Image/Build/squashfs
	$(call prepare_generic_squashfs,$(KDIR)/root.squashfs)
endef

define Image/Build
	$(call Image/Build/$(1),$(1))
	dd if=$(KDIR)/root.$(1) of=$(BIN_DIR)/$(IMG_PREFIX)-$(1)-root.img bs=2k conv=sync
endef

$(eval $(call BuildImage))
