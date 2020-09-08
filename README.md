# Iono Pi Max CAN interface driver

SocketCAN driver and device tree overlay for the CAN FD/CAN 2.0 controller (MCP2518FD) of [Iono Pi Max](https://www.sferalabs.cc/iono-pi-max/) - the industrial controller based on the Raspberry Pi Compute Module

Credit for the driver code and installation scripts to:
- https://github.com/marckleinebudde/linux
- https://github.com/Seeed-Studio/seeed-linux-dtoverlays

## Installation

Clone this repo:

    git clone --depth 1 https://github.com/sfera-labs/iono-pi-max-can-driver.git

Build and install the driver:

    cd iono-pi-max-can-fd-dtoverlay/mcp25xxfd
    sudo ./install.sh
    cd ..

Compile and install the Device Tree:

    dtc -@ -Hepapr -I dts -O dtb -o ionopimax-can-fd.dtbo ionopimax-can-fd.dts
    sudo cp ionopimax-can-fd.dtbo /boot/overlays/
    
Enable it by adding the following line to `/boot/config.txt`:

    dtoverlay=ionopimax-can-fd
    
Optionally remove the downloaded repo:

    cd ..
    sudo rm -r iono-pi-max-can-fd-dtoverlay

Reboot:

    sudo reboot

## Usage

You can check the driver has correctly initialized the controller with:

    dmesg | grep spi
    
the result should contain:

    mcp25xxfd spi0.0 can0: MCP2518FD rev0.0 (-RX_INT -MAB_NO_WARN +CRC_REG +CRC_RX +CRC_TX +ECC -HD m:20.00MHz r:18.50MHz e:0.00MHz) successfully initialized.

and by running:

    ifconfig -a
    
you should see a new interface named `can0`:

    can0: flags=193<NOARP>  mtu 16
            unspec 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  txqueuelen 10  (UNSPEC)
            RX packets 3  bytes 24 (24.0 B)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 3  bytes 24 (24.0 B)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
            device interrupt 166
            
You can now use the [SocketCAN](https://www.kernel.org/doc/Documentation/networking/can.txt) framework to control the interface.

## Examples

Initialize `can0` specifying some communication parameters and enabling the CAN FD protocol:

    sudo ip link set can0 up type can bitrate 1000000 dbitrate 8000000 restart-ms 1000 berr-reporting on fd on
    sudo ifconfig can0 txqueuelen 65536

Dump data from the bus:

    candump can0

Generate random traffic:

    cangen can0 -mv
