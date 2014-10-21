require 'stdlib/base'
require 'stdlib/uboot'
require 'stdlib/linux'

# speed up building by not having to unpack the build tools each time
#ENV['CROSS_COMPILE'] = '/home/pi/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin/arm-linux-gnueabihf-'

package :rpi_dt_linux do
  raise "RPI_DT_LINUX_BRANCH is not set" unless VAR['RPI_DT_LINUX_BRANCH']

  VAR['RPI_DT_LINUX_REF'] ||= github_get_head('pietrushnic/rpi-dt-linux', VAR['RPI_DT_LINUX_BRANCH'])

  if VAR['RPI_DT_LINUX_LOCAL'].nil?
    # download source
    #
    github_tarball "pietrushnic/rpi-dt-linux", 'linux', 'RPI_DT_LINUX'
    # we're not vanilla, but the Makefile can't determine that, so make it explicit
    target :unpack do
      File.write workdir('linux/.scmversion'), '+'
    end
  else
    # use local git repo in Rakefile directory
    #
    # NOT TESTED
    #
    target :unpack do
      ln_s workdir("#{VAR['RPI_DT_LINUX_LOCAL']}"), workdir('linux')
    end
  end

  config 'MMC_BCM2835', :enable
  config 'DMA_BCM2835', :enable

  ENV['LINUX_DEFCONFIG'] ||= 'bcm2835_defconfig'
  config ['CONFIG_IKCONFIG', 'CONFIG_IKCONFIG_PROC'], :enable
  config 'PROC_DEVICETREE', :enable

  target :kbuild do
    post_install <<EOM
cp "${FW_REPOLOCAL}/zImage" "${FW_PATH}/"

EOM
	end

  target :build do
    dst = workdir 'out'
    ksrc = workdir 'linux'
    msrc = workdir 'modules'

    cp(ksrc + "/arch/arm/boot/zImage", dst)
    sh "cp #{ksrc}/arch/arm/boot/dts/*.dtb #{dst}"
    mkdir_p(dst + "/modules")
    sh "cp -r #{msrc}/lib/modules/* #{dst}/modules/" unless FileList["#{msrc}/lib/modules/*"].empty?
    sh "cp -r #{msrc}/lib/firmware #{dst}/" unless FileList["#{msrc}/lib/firmware/*"].empty?
    cp(ksrc + "/Module.symvers", dst)
    mkdir_p(dst + "/extra")
    cp(ksrc + "/System.map", dst + "/extra/")
    cp(ksrc + "/.config", dst + "/extra/")
  end
end


release 'rpi-dt-linux' => [:issue106, :raspberrypi_tools, :raspberrypi_firmware, :uboot_bcm2835, :rpi_dt_linux] do

  VAR['RPI_DT_LINUX_BRANCH'] = 'rpi-3.16.y'

  ENV['COMMIT_MESSAGE'] = "3.16.y.5 release (#{VAR['KERNEL_RELEASE']})"
  Readme.desc { "Raspberry Pi Linux kernel #{VAR['KERNEL_RELEASE']} (ARCH_BCM2835) with additional patches." }
  Readme.body = """

Changelog
---------
2014-10-21:
* Fix bcm2835-mbox issues with checking wrong mailbox
* Enable DMA_BCM2835

2014-10-15:
* Rebase to 3.16.6

2014-10-15:
* Third release
* bcm2835-mbox, bcm2835-cpufreq and bcm2835-thermal drivers based on Lubomir Rintel [work](https://github.com/hackerspace/rpi-linux/commits/lr-raspberry-pi-new-mailbox)

2014-10-12:
* Rebase to 3.16.5 - second release
* Built from GitHub repository [rpi-dt-linux](https://github.com/pietrushnic/rpi-dt-linux.git) - `rpi-3.16.y` branch

2014-09-28:
* First release

Links
* [Upstreaming](https://github.com/raspberrypi/linux/wiki/Upstreaming)
* [Device Tree on ARCH_BCM2708](https://github.com/raspberrypi/linux/wiki/Device-Tree-on-ARCH_BCM2708)
"""
end
