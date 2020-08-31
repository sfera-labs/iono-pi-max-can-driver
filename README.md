git clone https://github.com/sfera-labs/iono-pi-max-can-fd-dtoverlay
cd iono-pi-max-can-fd-dtoverlay
cd mcp25xxfd
sudo ./install.sh
cd ..
dtc -@ -Hepapr -I dts -O dtb -o ionopimax-can-fd.dtbo ionopimax-can-fd.dts
sudo cp ionopimax-can-fd.dtbo /boot/overlays/
sudo nano /boot/config.txt
dtoverlay=ionopimax-can-fd
sudo reboot


$ dmesg | grep spi
[    8.713847] mcp25xxfd spi0.0 can0: MCP2518FD rev0.0 (-RX_INT -MAB_NO_WARN +CRC_REG +CRC_RX +CRC_TX +ECC -HD m:20.00MHz r:18.50MHz e:0.00MHz) successfully initialized.

$ ifconfig -a
can0: flags=193<UP,RUNNING,NOARP>  mtu 16
        unspec 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  txqueuelen 10  (UNSPEC)
        RX packets 3  bytes 24 (24.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 3  bytes 24 (24.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        device interrupt 166 

$ sudo ip link set can0 up type can bitrate 500000

$ dmesg | grep can0
[    8.713847] mcp25xxfd spi0.0 can0: MCP2518FD rev0.0 (-RX_INT -MAB_NO_WARN +CRC_REG +CRC_RX +CRC_TX +ECC -HD m:20.00MHz r:18.50MHz e:0.00MHz) successfully initialized.
[   69.953439] IPv6: ADDRCONF(NETDEV_CHANGE): can0: link becomes ready

On receiver:
$ candump can0

On sender:
$ sudo cansend can0 555#0000000000000000