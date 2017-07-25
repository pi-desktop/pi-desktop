# How to create partition and format SSD

If you use ppp-hdclone(piclone) to move the exist file system to you SSD you **do not** need to create partition table and format the partition because piclone can do these automatically. But if you just use the SSD as an USB storage device you need to create partition table and format it youself. In general, the new SSD you bought has not been formatted. For details please read this document.

We can partition/format the mSATA SSD on Raspberry Pi or other Linux PC.The OS should be based on Linux.such as debian.

Note:  If you’re connecting the mSATA SSD add-on board to a computer other than the Raspberry Pi inside the Pi Desktop, then you will need two micro-USB cables, one for power and the other for USB communication. Both of the tww micro-SUB cable should be plug into the USB host.Then Press the Power-On switch to make the mSATA be recognized by the USB host. This will also power on the Raspberry Pi if it is still connected.

## Disk Ddentity
Firstly you need to know the device identifiers.
The Raspbian OS is based upon Debian Linux, and as such supports the majority of commands and functions that you would use to mount drives within that OS (these typically involve fstab - https://wiki.debian.org/fstab, and pmount - https://wiki.debian.org/pmount ).

From the Graphical User Interface (GUI)
Depending on your version of Raspbian, and how you have it configured, when you boot your Raspberry Pi to the GUI and then connect the attached mSATA SSD, you will be prompted what to do with your “Removable media” that has been inserted, and the drive will be automatically mounted.

It is possible that the GUI will not behave in this way, and instead you will have a transparent icon representing the drive on your desktop, which you can then double-click with the left mouse button and Raspbian will attempt to mount it.

From the Command Line Interface (CLI)
When mounting the drive from the GUI, you will have made the drive accessible from the CLI. Typically this is located within the folder ‘/media’.

However, sometimes we can’t use the GUI, or merely we don’t want to. To mount the drive we must first know how Linux is referring to it. Linux has the majority of its hardware listed under the ‘/dev’ folder structure, with connected devices typically using the format of ‘/dev/sd<x>’ where <x> is a letter. These can even extend further with ‘/dev/sd<x><y>’ where <y> is the number of the partition(s) on the device.

There are a few commands in which we can determine what the device is of a USB device we connect, first, connect the USB device, and then type the following:

> $ sudo dmesg

This will tell you basically the contents of a system log which gives you information about the device you have just plugged in. You can also issue the following commands:

> $ sudo lsusb

Which will tell you about the device identifiers, and also:

> $ sudo lsblk

This command will tell you the /dev/ mount points.

You will now need somewhere to mount the drive to. To make a directory and set the permissions onto it by issuing the following commands:

> $ sudo mkdir /media/ssd

> $ sudo chmod 755 /media/ssd

Once you know the device mount point, you can then issue the following command:

> $ sudo mount /dev/sd<x><y> /media/ssd

Note:  Mounting will only work if the mSATA SSD has a file system and partitions setup (see ‘Imaging and Setting Up Partitions’ in this document.), you will want to mount the relevant partition (which has a number, such as /dev/sdb1) as opposed to the device itself (/dev/sdb). 

The Raspberry Pi Foundation’s official magazine, The Mag Pi has a good article on this:

[https://www.raspberrypi.org/magpi/connecting-disks-with-the-command-line/](https://www.raspberrypi.org/magpi/connecting-disks-with-the-command-line/)

## Pattion & Format

There are two tools we recommend: gparted from GUI and fdisk-mkfs from CLI.

### 1 gparted
An easy to use utility for managing the partitions on the drive is ‘gparted’ from the Raspbian (OS) (for example if you booted from the SD Card):

[http://gparted.org/](http://gparted.org/)

This is a graphical user interface (GUI) tool that can be used to manage the partitions on your drives, connected via USB or otherwise. It can be installed via the apt package manager from the command line interface (CLI):

> $ sudo apt-get update    
> $ sudo apt-get install gparted    

Then you can either run it from the menu of your Raspbian OS, or from the CLI:

> $ sudo gparted

Start the GUI and follow the hint.

### 2 fdisk-mkfs
Assume that the un-partioned disk is mounted at /dev/sdc.
> sudo fdisk /dev/sdc

Just follow the hint to create nwe partion-table and create new partion. If you have any queries, you can read the fdisk help documentation.

>  Command (m for help): m    
>  Command action    
>    a   toggle a bootable flag    
>    b   edit bsd disklabel    
>    c   toggle the dos compatibility flag    
>    d   delete a partition    
>    l   list known partition types    
>    m   print this menu    
>    n   add a new partition    
>    o   create a new empty DOS partition table    
>    p   print the partition table    
>    q   quit without saving changes    
>    s   create a new empty Sun disklabel    
>    t   change a partition's system id    
>    u   change display/entry units    
>    v   verify the partition table    
>    w   write table to disk and exit    
>    x   extra functionality (experts only)    
>    
>  Command (m for help):     


You can do it step by step and it also can be done by just one command.
For expmple this comnand can make two patiron on the disk:

>  $ echo -e "o\nn\np\n1\n\n+64M\na\n1\nt\nc\nn\np\n2\n\n\nw\n" | sudo fdisk /dev/sdc    
>  $ ls /dev/sdc*    
>  sdc1 sdc2

As you see sdc1 and sdc2 appears on /dev/, which tells that two partions has been created on sdc.

After partion you need to format the partion. Then we can use mkfs tool.Type mkfs then click Tab you can see the following types of filesystem formats:

> $ mkfs    
> mkfs          mkfs.cramfs   mkfs.ext3     mkfs.ext4dev  mkfs.minix    mkfs.ntfs         
> mkfs.bfs      mkfs.ext2     mkfs.ext4     mkfs.fat      mkfs.msdos    mkfs.vfat     

For example we choose fat to format the /dev/sdc1:

>  $ sudo mkfs.fat /dev/sdc1

With the default parameters and type Enter to finish the process. 

After all, remember type 'sync' to sync all data to the disk. This is very important for Linux OS.

### 3 The most simple way
Simply, you can write an image to the mSATA SSD, just as you would write an image to an SD Card. The Raspberry Pi Foundation has instructions on how you write images here:

[https://www.raspberrypi.org/documentation/installation/installing-images/](https://www.raspberrypi.org/documentation/installation/installing-images/)

Note:  If you write an image to your mSATA SSD then the data used and accessible from the OS will only be of the size of the image written, unless you resize the partitions. And the image you want to writte must be smaller than the disk size.


