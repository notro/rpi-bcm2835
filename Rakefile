require 'stdlib/base'
require 'stdlib/uboot'
require 'stdlib/linux'

# speed up building by not having to unpack the build tools each time
#ENV['CROSS_COMPILE'] = '/home/pi/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin/arm-linux-gnueabihf-'

package :rpi_bcm2835_linux => :kernelorg_linux do
  VAR['LINUX_DEFCONFIG'] = 'bcm2835_defconfig'

  # we're not vanilla, but the Makefile can't determine that, so make it explicit
  target :unpack do
    File.write workdir('linux/.scmversion'), '+'
  end

  # mailbox API
  patch '0001_mailbox__rename_pl320-ipc_specific_mailbox.h.patch'
  patch '0002_mailbox__Introduce_framework_for_mailbox.patch'
  patch '0003_doc__add_documentation_for_mailbox_framework.patch'
  patch '0004_dt__mailbox__add_generic_bindings.patch'

  # mailbox driver
  #patch '0010_'

  patch '0020_ARM__bcm2835__Enable_USB_DWC2_HOST_in_bcm2835_defconfig.patch'

#  # bcm2835-dma slave_sg patch
#  # patch '0030_'
#  config 'DMADEVICES', :enable
#  config 'DMA_BCM2835', :enable

  patch '0040_MMC__added_alternative_MMC_driver.patch'
  config 'MMC_BCM2835', :enable
  #config 'MMC_BCM2835_DMA', :enable
  #config 'MMC_BCM2835_PIO_DMA_BARRIER', :val, 2

  # bcm2835-mmc Device Tree node
  patch '0041_dt_bcm2835_add_mmc_node.patch'

  #config 'DYNAMIC_DEBUG', :enable
end


release 'rpi-bcm2835' => [:issue106, :raspberrypi_tools, :raspberrypi_firmware, :uboot_bcm2835, :rpi_bcm2835_linux] do

# download problems, use locally cached version
#VAR['UBOOT_REF'] = '0a26e1d6c394aacbf1153977b7348d1dff85db3f'

  VAR['KERNEL_ORG_VERSION'] ||= kernelorg_linux_latest
  info "KERNEL_ORG_VERSION = #{VAR['KERNEL_ORG_VERSION']}"

  VAR['FW_BRANCH'] = 'rpi-bcm2835'

  ENV['COMMIT_MESSAGE'] = "First release (#{VAR['KERNEL_RELEASE']})"
  Readme.desc { "Raspberry Pi Linux kernel #{VAR['KERNEL_RELEASE']} (ARCH_BCM2835) with additional patches." }
  Readme.body = """

Changelog
---------
2014-09-28:
* First release

Links
* [Upstreaming](https://github.com/raspberrypi/linux/wiki/Upstreaming)
* [Device Tree on ARCH_BCM2708](https://github.com/raspberrypi/linux/wiki/Device-Tree-on-ARCH_BCM2708)

"""
end

