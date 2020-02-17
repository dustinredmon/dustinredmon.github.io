---
layout: post
title: Blog 15 - Attach and Mount an EBS volume to an EC2 Instance
---

In order to complete my lab for this week, I will need to attach and mount an EBS volume to an EC2 instance. While this is a very simple task, it took me a really long time to realize that attaching an EBS volume is different than mounting and EBS volume. I thought the words "attach" and "mount" were being used interchangeably. They were not. 

To put it simple, when you attach an EBS volume, you are simply assigning it to an EC2 instance to be available to use. Attaching an EBS volume does not mean that it is actually being used by an instance. In order for a volume to actually be used by an instance, it needs to be mounted first. This is usually done by assigning the volume to a folder on the EC2 instance, which can be used as an access point to the volume to read or write files to it. 

When you create an instance, an EBS volume is automaticlly attached and mounted to store the file system of your instance. This is the default volume for your instance. Default volumes will remain mounted everytime you restart your instance. You can attach as many EBS volumes as you like to an instance. However, only default EBS volumes are automatically mounted when an EC2 instance is restarted. Typically we would want all of our volumes mounted, every time an instance is restarted. In order to automatically mount additional EBS volumes to an instance on startup, we will need to edit the "fstab" file, which we ill cover in this tutorial.

Step 1: Open your EC2 console in AWS, and select the "Volumes" Section in the navigation pane. Create a new volume of your preferred size and type.

Step 2: Select the created volume, right click and select the “attach volume” option. Select an instance from the drop menu that you want to attch the volume to.

Step 3: Now, login to your ec2 instance and list the available disks using the following command. This command will list the disk you attached to your instance.

    lsblk
    
Step 4: Check if the volume has any data using the following command. If the output shows “/dev/xvdf: data”, it means your volume is empty.

    	sudo file -s /dev/xvdf

Step 5: Format the volume to ext4 filesystem  using the following command.

    sudo mkfs -t ext4 /dev/xvdf

Step 6: Create a directory of your choice to mount our new ext4 volume. I am using the name “lab1volume”.

    sudo mkdir /newvolume

Step 7: Mount the volume to “lab1volume” directory using the following command.

    sudo mount /dev/xvdf /newvolume/

Step 8: cd into newvolume directory and check the disk space for confirming the volume mount.

    cd /newvolume
    df -h .

The above command would show the free space in the newvolume directory. To unmount the volume, you have to use the following command.

    umount /dev/xvdf

Step 9: Enable EBS Automount on Reboot

Like I pointed out earlier, by default on every reboot the  EBS volumes other than root volume will get unmounted. To enable automount, you need to make an entry in the /etc/fstab file. Open /etc/fstab file and make an entry in the following format:

    device_name mount_point file_system_type fs_mntops fs_freq fs_passno

For example,

    /dev/xvdf       /newvolume   ext4    defaults,nofail        0       0

Execute the following command to check id the fstab file has any error.

    sudo mount -a

If the above command shows no error, it means you correctly inserted you fstab entry. Now, on every reboot the extra EBS volumes will get mounted automatically.
