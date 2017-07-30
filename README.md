# Raising the dead - Undeleting files in ext4

The first thing you should do is to figure out what you are up against. What is the OS you are running on that specific machine? What filesystem are you using?

```
sudo uname -a
sudo fdisk -l
sudo blkid
```
After you are done with gathering this information, shutdown the machine. 
This will ensure nothing will overwrite the files you just deleted, and it will thus increase the probability of a 
successful recovery.

## Get the right tools
Given the situation, your best option is to boot the machine using a live CD. 
I recommend using BackTrack, which comes bundled with a few useful forensics tools.

In my particular case the filesystem I was using was ext4. This meant that my only option was to 
run extundelete (conveniently bundled into backtrack).

## Convenience setup
After burning the ISO (or using a bootable usb pen), boot your computer into BackTrack.

For rack-mountable servers that you have no easy access to, I recommend enabling remote ssh:

```
service ssh start
sshd-generate
passwd root
```
These commands will allow you to ssh remotely into the BackTrack liveCD.

## LVM Partition?!
If your partition is a LVM Partition, these are the steps you will need to execute to be able to successfully mount it:
```
sudo apt-get install lvm2
sudo modprobe dm-mod
sudo vgscan
sudo vgchange -ay VOLUME_NAME
sudo lvs
sudo mkdir /mnt/thisblows
sudo mount /dev/VOLGROUP/VOLUME_NAME /mnt/thisblows -o ro,user
```
## Actually undeleting stuff
After everything is mounted you can now run extundelete:

`extundelete /dev/mapper/VOLUME_NAME --restore-all`

This command will create a RECOVERED_FILES folder in the your current directory. Make sure you are in /tmp or some other LiveCD in-memory partition. After you recover the files, scp them off to a remote machine and you are done.

