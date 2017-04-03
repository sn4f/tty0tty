
# tty0tty - linux null modem emulator v1.2 


The tty0tty directory tree is divided in:

  **module** - linux kernel module null-modem  
  **pts** - null-modem using ptys (without handshake lines)


## Null modem pts (unix98): 

  When run connect two pseudo-ttys and show the connection names:
  
  (/dev/pts/1) <=> (/dev/pts/2)  

  the connection is:
  
  TX -> RX  
  RX <- TX  



## Module:

 The module is tested in kernel 3.10.2 (debian) 

  When loaded, create 24*2 ttys interconnected:
  
  /dev/tnt0  <=>  /dev/tnt1  
  /dev/tnt2  <=>  /dev/tnt3  
  ...
  /dev/tnt[2n] <=> /dev/tnt[2n+1]
  ...
  /dev/tnt24  <=>  /dev/tnt25
  

  the connection is:
  
  TX   ->  RX  
  RX   <-  TX  
  RTS  ->  CTS  
  CTS  <-  RTS  
  DSR  <-  DTR  
  CD   <-  DTR  
  DTR  ->  DSR  
  DTR  ->  CD  
  

## Requirements:

  For building the module kernel-headers or kernel source are necessary.
  
  Installing linux header for raspbian: 
  
  <script src="https://gist.github.com/garronej/551aa780f6b4f11788b791c6c578a0ed.js"></script>

## Installation:

Download the tty0tty package from one of these sources:
Clone the repo https://github.com/garronej/tty0tty

```
sudo su && cd /usr/src
git clone https://github.com/garronej/tty0tty
```



Build the kernel module from provided source

```
cd tty0tty-1.2/module
make
```

Copy the new kernel module into the kernel modules directory

```
sudo cp tty0tty.ko /lib/modules/$(uname -r)/kernel/drivers/misc/
```

Load the module

```
sudo depmod
sudo modprobe tty0tty
```

You should see new serial ports in ```/dev/``` (```ls /dev/tnt*```)
Give appropriate permissions to the new serial ports

```
sudo chmod 666 /dev/tnt*
```

You can now access the serial ports as /dev/tnt0 (1,2,3,4 etc) Note that the consecutive ports are interconnected. For example, /dev/tnt0 and /dev/tnt1 are connected as if using a direct cable.

Persisting across boot:

edit the file /etc/modules (Debian) or /etc/modules.conf

```
nano /etc/modules
```
and add the following line:

```
tty0tty
```

Note that this method will not make the module persist over kernel updates so if you ever update your kernel, make sure you build tty0tty again repeat the process.

## Debian package

In order to build the dkms Debian package

```
sudo apt-get update && sudo apt-get install -y dh-make dkms build-essential
debuild -uc -us
```

## Contact

For e-mail suggestions :  lcgamboa@yahoo.com
