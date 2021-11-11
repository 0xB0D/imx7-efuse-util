# imx7-efuse-util
A simple python script to flash the imx7 OTP fuses with key material, lock the part to secure mode and read-back OTP state
Burning SRK fuses from Linux
```
root@imx7s-warp:/media# ./imx7-efuse-util.py -k SRK_1_2_3_4_fuse.bin
Write key values in SRK_1_2_3_4_fuse.bin to SRK fuses => /sys/bus/nvmem/devices/imx-ocotp0/nvmem y/n y
Key 0 0x0e250e03
Key 1 0x9d560868
Key 2 0xa22f48c7
Key 3 0x02812e14
Key 4 0xdde453fc
Key 5 0x7b42dc98
Key 6 0xc2c015d8
Key 7 0x733a36f5
```
Where SRK_1_2_3_4_fuse.bin is a fuse file output by the CST tool provided by NXP

A future option would be to reboot the system and interrogate the HAB log to ensure U-Boot validation was successful prior to placing part into CONFIG_SEC mode. This is not implemented today.
Burning CONFIG_SEC fuse from Linux
Burning the CONFIG_SEC fuse puts the part into Closed mode - it will not be possible to boot a first stage bootloader that has not be signed and authenticated against the SRK fuses
```
root@imx7s-warp:/media# ./imx7-efuse-util.py -l
Secure fuse keys
Bank 6
        0x0e250e03
        0x9d560868
        0xa22f48c7
        0x02812e14
Bank 7
        0xdde453fc
        0x7b42dc98
        0xc2c015d8
        0x733a36f5
Lock part into secure-boot mode with above keys ?  y/n y
Are you REALLY sure ? y/n y
```
# Displaying SRK and CONFIG_SEC settings
```
root@imx7s-warp:/media# ./imx7-efuse-util.py -s
Path : /sys/bus/nvmem/devices/imx-ocotp0/nvmem
Boot Fuse settings
OCOTP_BOOT_CFG0 = 0x16002820
        FORCE_COLD_BOOT = 0
        BT_FUSE_SEL     = 1
        DIR_BT_DIS      = 0
        SEC_CONFIG      = 1
        Boot Mode       = MMC/eMMC
Secure fuse keys
Bank 6
        0x0e250e03
        0x9d560868
        0xa22f48c7
        0x02812e14
Bank 7
        0xdde453fc
        0x7b42dc98
        0xc2c015d8
        0x733a36f5
```
# Secure boot flow
After fusing the SRK keys and SEC_CONFIG fuses the i.MX7 part is in the Closed-mode state and will only execute a signed first-stage bootloader.
