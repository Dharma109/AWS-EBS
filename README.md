# AWS-EBS

Amazon EBS volumes

An Amazon EBS volume is a durable, block-level storage device that you can attach to your instances. After you attach a volume to an instance, you can use it as you would use a physical hard drive. EBS volumes are flexible. For current-generation volumes attached to current-generation instance types, you can dynamically increase size, modify the provisioned IOPS capacity, and change volume type on live production volumes.

You can use EBS volumes as primary storage for data that requires frequent updates, such as the system drive for an instance or storage for a database application. You can also use them for throughput-intensive applications that perform continuous disk scans. EBS volumes persist independently from the running life of an EC2 instance.

You can attach multiple EBS volumes to a single instance. The volume and instance must be in the same Availability Zone. Depending on the volume and instance types, you can use Multi-Attach to mount a volume to multiple instances at the same time.

Amazon EBS provides the following volume types: General Purpose SSD (gp2 and gp3), Provisioned IOPS SSD (io1 and io2), Throughput Optimized HDD (st1), Cold HDD (sc1), and Magnetic (standard). They differ in performance characteristics and price, allowing you to tailor your storage performance and cost to the needs of your applications. For more information, see Amazon EBS volume types.

Types of EBS Volumes:
SSD: This storage type is suitable for small chunks of data that requires fast I/Ops. SSDs can be used as root volumes for EC2 instances.

General Purpose SSD (GP2)
Offers a single-digit millisecond latency.
Can provide 3000 IOps burst.
IOps speed is limited from 3-10000 IOps.
The throughput of these volumes is 128MBPS up to 170GB. After which throughput increases 768KBPS per GB and peaks at 160MBPS.
Provisioned IOPS SSD (IO1)
These SSDs are IO intensive.
Users can specify IOPS requirement during creation.
Size limit is 4TB-16TB
According to AWS claims “These volumes, if attached to EBS optimized instances will deliver IOPS defined within 10% 99.9% times of the year”
Max IOPS speed is 20000.
HDD: This storage type is suitable for Big Data chunks and slower processing. These volumes cannot be used as root volumes for EC2. AWS claims that “These volumes provide expected throughput 99.9% times of the year”

Cold HDD (SC1)
SC1 is the cheapest of all EBS volume types. It is suitable for large, infrequently accessed data.
Max Burst speed offered is 250 Mbps
Throughput optimized HDD (ST)
Suitable for large, frequently accessed data.
Burst speed ranges from 250 MBPS to 500 MBPS.

### Add disk to EC2 :

> List the disk on the instance 

[root@ip-192-168-10-144 ~]# lsblk
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0   8G  0 disk
└─xvda1 202:1    0   8G  0 part /


> after attaching the disk from volume dash board -> list the blk disk 

[root@ip-192-168-10-144 ~]# lsblk
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0   8G  0 disk
└─xvda1 202:1    0   8G  0 part /
xvdb    202:16   0   5G  0 disk


-> DF -h will list you the mouted disk on the instance 

[root@ip-192-168-10-144 ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        468M     0  468M   0% /dev
tmpfs           477M     0  477M   0% /dev/shm
tmpfs           477M  512K  476M   1% /run
tmpfs           477M     0  477M   0% /sys/fs/cgroup
/dev/xvda1      8.0G  1.9G  6.2G  24% /
tmpfs            96M     0   96M   0% /run/user/0


-> Format the volume to the ext4 filesystem using the following command.
sudo mkfs -t ext4 /dev/xvdb

[root@ip-192-168-10-144 ~]# mkfs -t ext4 /dev/xvdb
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310720 blocks
65536 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736

Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done


--> Mount the formated disk to a new directory 

# Create Directory 

[root@ip-192-168-10-144 ~]# mkdir /mydisk


# mount the directory 

[root@ip-192-168-10-144 ~]#  mount /dev/xvdb /mydisk
[root@ip-192-168-10-144 ~]#

# Check if df -h is showing the mounted disk 

[root@ip-192-168-10-144 ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        468M     0  468M   0% /dev
tmpfs           477M     0  477M   0% /dev/shm
tmpfs           477M  512K  476M   1% /run
tmpfs           477M     0  477M   0% /sys/fs/cgroup
/dev/xvda1      8.0G  1.9G  6.2G  24% /
tmpfs            96M     0   96M   0% /run/user/0
/dev/xvdb       4.8G   24K  4.6G   1% /mydisk
[root@ip-192-168-10-144 ~]#

