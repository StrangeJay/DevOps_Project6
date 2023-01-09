# WEB SOLUTION WITH WORDPRESS 

You are progressing in practicing to implement web solutions using different technologies. As a DevOps engineer you will most probably encounter [PHP](https://www.php.net/)-based solutions since, even in 2021, it is the dominant web programming language used by more websites than any other programming language.
In this project you will be tasked to prepare storage infrastructure on two Linux servers and implement a basic web solution using [WordPress](https://en.wikipedia.org/wiki/WordPress). WordPress is a free and open-source content management system written in **PHP** and paired with **MySQL** or **MariaDB** as its backend Relational Database Management System (RDBMS).  

Project 6 consists of two parts:  
1. Configure storage subsystem for Web and Database servers based on Linux OS. The focus of this part is to give you practical experience of working with disks, partitions and volumes in Linux.  

2. Install WordPress and connect it to a remote MySQL database server. This part of the project will solidify your skills of deploying Web and DB tiers of Web solution.  


As a DevOps engineer, your deep understanding of core components of web solutions and your ability to troubleshoot them will play an essential role in your further progress and development.  

## Three-tier Architecture  
Generally, web, or mobile solutions are implemented based on what is called the **Three-tier Architecture.**
Three-tier Architecture is a client-server software architecture pattern that comprise of 3 separate layers. Namely: The presentation layer, the Business layer and the data Access or Management Layer(DAL). 

![Screenshot_20230107_144555](https://user-images.githubusercontent.com/105195327/211153921-1e306de2-0cd2-47e0-ba37-730d4d85dfdc.png)

1. **Presentation Layer (PL):** This is the user interface such as the client server or browser on your laptop.  
2. **Business Layer (BL):** This is the backend program that implements business logic. Application or Webserver  
3. **Data Access or Management Layer (DAL):** This is the layer for computer data storage and data access. [Database Server](https://www.computerhope.com/jargon/d/database-server.htm) or File System Server such as [FTP server](https://titanftp.com/2018/09/11/what-is-an-ftp-server/), or [NFS Server](https://searchenterprisedesktop.techtarget.com/definition/Network-File-System).  
In this project, you will have the hands-on experience that showcases **Three-tier Architecture** while also ensuring that the disk used to store files on the Linux servers are adequately partitioned and managed through programs such as $\color{pink}{gdisk}$ and $\color{pink}{LVM}$ respectively.
You will be working working with several storage and disk management concepts, to have a better understanding, watch following video:  
[Disk management in Linux](https://darey.io/courses/step-12-logical-volume-management/lessons/lesson-1-storage-management/topic/create-linux-partitions-with-fdisk/)  

> **Note** We are gradually introducing new AWS elements into our solutions, but do not be worried if you do not fully understand AWS Cloud Services yet, there are Cloud focused projects ahead where we will get into deep details of various Cloud concepts and technologies – not only AWS, but other Cloud Service Providers as well.  

## Your 3-Tier Setup  
1. A Laptop or PC to serve as a client
2. An EC2 Linux Server as a web server (This is where you will install WordPress)
3. An EC2 Linux server as a database (DB) server  

Use $\color{pink}{RedHat}$ OS for this project  

If uou've forgotten how to spin an EC2 instance, please refer to [Project 2](https://github.com/StrangeJay/DevOps-Project2). 

In previous projects we used ‘Ubuntu’, but it is better to be well-versed with various Linux distributions, thus, for this projects we will use very popular distribution called ‘RedHat’ (it also has a fully compatible derivative – CentOS)  

> **Note:** for Ubuntu server, when connecting to it via SSH/Putty or any other tool, we used $\color{pink}{ubuntu}$ user, but for RedHat you will need to use $\color{pink}{ec2-user}$ user. Connection string will look like `ec2-user@<Public-IP>`
Let us get started!  
---
## LAUNCH AN EC2 INSTANCE THAT WILL SERVE AS “WEB SERVER” 

### Step 1 - Prepare a web server 
  
- Launch an EC2 instance that will serve as "Web server". Create 3 volumes in the smae AZ as your Web Server EC2, each of 10GB.  
  
Learn How to add an EBS volume to an EC2 instance [here](https://www.youtube.com/watch?v=HPXnXkBzIHw)   
  
- Attach all three volumes one by one to your Web Server EC2 instance  
https://user-images.githubusercontent.com/105195327/211216013-97e0006b-8867-4328-bd86-b2b6bbb9a391.mp4   

- Open up the Linux terminal to begin configuration  
- Use lsblk command to inspect what block devices are attached to the server. 
 ![Screenshot_20230108_211409](https://user-images.githubusercontent.com/105195327/211217066-073f89f8-d9a8-4fde-8488-cfa9856709e0.png)  
  
 Notice names of your newly created devices. All devices in Linux reside in /dev/ directory. Inspect it with ls /dev/ and make sure you see all 3 newly created block devices there – their names will likely be $\color{pink}{xvdf\, xvdh\, xvdg}$.  
![Screenshot 2023-01-08 211523](https://user-images.githubusercontent.com/105195327/211217224-61ea2d8e-c1fd-4394-ae58-9343e1710f01.png)  

- Use `df -h` command to see all mounts and free space on your server  
 ![Screenshot_20230108_211932](https://user-images.githubusercontent.com/105195327/211217234-db369948-09a5-4f72-b99b-90446162fcca.png)  


- Use $\color{pink}{gdisk} utility to create a single partition on each of the 3 disks  
 `sudo gdisk /dev/xvdf`   
  
![partition table](https://user-images.githubusercontent.com/105195327/211217510-f8b1c189-a033-4587-9003-f96508bac232.png)   

![create new partition](https://user-images.githubusercontent.com/105195327/211218379-48363c3d-8f51-4ebb-8b25-e57322cbad77.png)
  
![Screenshot_20230108_214612](https://user-images.githubusercontent.com/105195327/211218393-365aed60-2de5-4b49-9b1a-998c2b0e793c.png)

  
![partition contd](https://user-images.githubusercontent.com/105195327/211217516-2052bc6f-203f-49cc-928b-51d3842af50e.png)   
  
![Screenshot_20230108_212744](https://user-images.githubusercontent.com/105195327/211217535-ab1bbe84-6878-4b20-b791-1b2624ee7a3a.png)   

*Now, do the same for the remaining disks $\color{pink}{xvdh}$ `sudo gdisk /dev/xvdh` and $\color{pink}{xvdg}$ `sudo gdisk /dev/xvdg`*  

- Use $\color{pink}{lsblk}$ utility to view the newly configured partition on each of the 3 disks.   
![Screenshot_20230108_215255](https://user-images.githubusercontent.com/105195327/211218563-0579e37e-59c6-4874-9faa-403b3ba826f9.png)   

- Install $\color{blue}{lvm2}$ package using `sudo yum install lvm2`. Run sudo lvmdiskscan command to check for available partitions.   
  
![Screenshot_20230108_215532](https://user-images.githubusercontent.com/105195327/211218651-ec07369d-f886-4d4d-8b63-020bdceaefe1.png)   

> **Note**  Previously, in Ubuntu we used the `apt` command to install packages, in RedHat/CentOS a different package manager is used, so we shall use yum command instead.   
  
- Use $\color{pink}{pvcreate}$ utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM.  
> sudo pvcreate /dev/xvdf1  
> sudo pvcreate /dev/xvdg1  
> sudo pvcreate /dev/xvdh1  

![Screenshot_20230108_220002](https://user-images.githubusercontent.com/105195327/211218824-e72e389d-e066-4616-88bc-b0edb05ac044.png)    
  
- Verify that your Physical volume has been created successfully by running $\color{pink}{sudo\ pvs}$  

![Screenshot_20230108_220022](https://user-images.githubusercontent.com/105195327/211218843-30e830b2-7487-4a4b-909d-7c0047ba80b7.png)   

- Use $\color{blue}{vgcreate}$ utility to add all 3 PVs to a volume group (VG). Name the VG **webdata-vg**   
> sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1   
  
- Verify that your VG has been created successfully by running $\color{pink}{sudo\ vgs}$  

![Screenshot_20230109_092255](https://user-images.githubusercontent.com/105195327/211265844-775d7626-828f-44b6-b729-d9429e1aa6fc.png)   

- Use $\color{blue}{lvcreate}$ utility to create 2 logical volumes. **apps-lv (Use half of the PV size)**, and **logs-lv Use the remaining space of the PV size.**  

> **NOTE** apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.  
 
> sudo lvcreate -n apps-lv -L 14G webdata-vg  
> sudo lvcreate -n logs-lv -L 14G webdata-vg  

![Screenshot_20230109_093025](https://user-images.githubusercontent.com/105195327/211266999-cd32e28d-7c6d-4cf1-a076-3029ed6b94f5.png)    

  
- Verify that your Logical Volume has been created successfully by running $\color{pink}{sudo\ lvs}$   

![Screenshot_20230109_093204](https://user-images.githubusercontent.com/105195327/211267457-053a5ed1-68d8-4761-a1ef-3a1b241d20fa.png)

   
- Verify the entire setup  
> sudo vgdisplay -v #view complete setup - VG, PV, and LV
> sudo lsblk  
  
![Screenshot_20230109_093404](https://user-images.githubusercontent.com/105195327/211267509-07863d14-f141-4fc0-8354-886786c54289.png)   

  
- Use $\color{pink}{mkfs\.ext4}$ to format the logical volumes with $\color{blue}{ext4}$ filesystem   
 
> sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
> sudo mkfs -t ext4 /dev/webdata-vg/logs-lv

![Screenshot_20230109_093734](https://user-images.githubusercontent.com/105195327/211267936-88da5529-2698-404c-b83b-4ee68f768e00.png)    

- Create /var/www/html directory to store website files    
> sudo mkdir -p /var/www/html  
  
Create **/home/recovery/logs** to store backup of log data
> sudo mkdir -p /home/recovery/logs   
  
- Mount /var/www/html on apps-lv logical volume
> sudo mount /dev/webdata-vg/apps-lv /var/www/html/
 
- Use $\color{blue}{rsync}$ utility to back up all the files in the log **directory /var/log** into **/home/recovery/logs** (This is required before mounting the file system)    
> sudo rsync -av /var/log/. /home/recovery/logs/   
  
- Mount **/var/log** on **logs-lv** logical volume.  
> **Note** *All the existing data on /var/log will be deleted. That is why step 15 above is very
important*  

> sudo mount /dev/webdata-vg/logs-lv /var/log   
 
- Restore log files back into **/var/log** directory
> sudo rsync -av /home/recovery/logs/. /var/log   
 
- Update `/etc/fstab` file so that the mount configuration will persist after restart of the server.

## UPDATE THE `/ETC/FSTAB` FILE  
The UUID of the device will be used to update the /etc/fstab file;
> sudo blkid  
![uuid](https://user-images.githubusercontent.com/105195327/211276105-7853834a-d18b-4576-a511-caf992d1730f.png)   

> sudo vi /etc/fstab

- Update /etc/fstab in this format using your own UUID and rememeber to remove the leading and ending quotes.    
![Screenshot_20230109_103647](https://user-images.githubusercontent.com/105195327/211280246-9f630b77-5e1a-49de-b83a-5c0f396ecb21.png)   

- Test the configuration and reload the daemon
> sudo mount -a
> sudo systemctl daemon-reload
 
- Verify your setup by running df -h, output must look like this:  

![Screenshot_20230109_104934](https://user-images.githubusercontent.com/105195327/211280718-ab376281-ea3e-421e-b828-0918422dad0b.png)   
---
### Step 2 - Prepare the Database Server  
- Launch a second RedHat EC2 instance that will have a role – **‘DB Server’**  
- Repeat the same steps as for the Web Server, but instead of apps-lv create db-lv and mount it to /db directory instead of /var/www/html/.  

### Step 3 — Install WordPress on your Web Server EC2  









  
  
  
  
  
  
  
  





