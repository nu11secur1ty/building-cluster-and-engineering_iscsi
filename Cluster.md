### Example of cluster with two nodes, install, and configuration on RHEL CentOS 6 from nu11secur1ty###



```
# Storage:
  storage 192.168.0.104

# Install in all nodes:
    node1 192.168.0.101
    yum install iscsi* -y
  
    node2 192.168.0.102
    yum install iscsi* -y

    node3
    ..........
```

# Connect node1 to storage:
```
  iscsiadm -m discovery -t st -p 192.168.0.104
  chkconfig iscsi on
  iscsiadm -m node -T (storage target..exam: ian.2015-05.com.example:apache.target1) -p 192.168.0.104 -l
```
# Connect node2 to storage:
```  
  iscsiadm -m discovery -t st -p 192.168.0.104
  chkconfig iscsi on
  iscsiadm -m node -T (storage target..exam: ian.2015-05.com.example:apache.target1) -p 192.168.0.104 -l
  
    check:
    node1:
    lsblk 
         output example:
         sdb               8:16   0    50G  0   disk
    
    node2:
    lsblk 
         output example:
         sdb               8:16   0    50G  0   disk
```

### device-mapper for multipath issue:###
    # NOTE: The time on every machine should not be a different
```
  
  node1:
  ```
       yum install device-mapper* -y
       mpathconf --enable
       /etc/init.d/multipathd restart
       chkconfig multipathd on
       lsblk
  
           # create partition for iscsi disk, then partprobes:
         fdisk -cu /dev/mapper/mpathd
         p
         n
         p
         1
         enter
         p
         w
         partprobe /dev/mapper/mpathd
         lsblk
           # see after partprobe the new dm-3 comes
         mkfs.ext4 /dev/mapper/mpathbp1
         partprobe /dev/mapper/mpathd
         yum install http* -y
         service httpd restart;chkconfig httpd on 
           # mount the disk with apache document root
         mount /dev/mapper/mpathbp1 /var/www/html/
           # Check mount points
         df -hT 
           # Create index.html file for node1 and node2
         touch /var/www/html/index.html
         vim /var/www/html/index.html
             - add: <h1>This is a test for apache server on share storage ISCSI disk.</h1> =)
           # Check on web browser. NOTE: To see node2 execute partprobe directly in node2
             (partprobe /dev/mapper/mpathb & mount /dev/mapper/mpathbp1 /var/www/html/).
             node1.example.com
             See if everything is mounted:
         df -h
           # If you want to stop the service, you have to "umount" manualy. This is recommended:.
         umount /var/www/html/ 
           # Start the cluster packages, this is recommended:
         yum install ricci* -y
         yum install luci* -y
         passwd ricci
         /etc/init.d/ricci restart;chkconfig ricci on
         /etc/init.d/luci restart
           # NOTE: The time on every machine should not be a different
           # Check:
         https://node1.example.com:8084/cluster#s
           # Follow the steps to create a cluster
           # Clear memory cache after setup
         echo 3 > /proc/sys/vm/drop_caches
           # Follow the steps to create a cluster and services...
```
  node2:
  ```
       yum install device-mapper* -y
       mpathconf --enable
       /etc/init.d/multipathd restart
       chkconfig multipathd on
       lsblk
       partprobe /dev/mapper/mpathd
       lsblk
           # see after partprobe the new dm-3 comes
           # partprobe again
       partprobe /dev/mapper/mpathd
           # Check storage as for apache web server
       yum install http* -y
       service httpd restart;chkconfig httpd on
       partprobe /dev/mapper/mpathb
       mount /dev/mapper/mpathbp1 /var/www/html/
           # Check node2.example.com
       df -h
           # If you want to stop the service, you have to "umount" manualy.
       umount /var/www/html/, this is recommended
           # Start the cluster packages:
       yum install ricci* -y 
       yum install luci* -y 
       /etc/init.d/ricci restart;chkconfig ricci on
           # NOTE: The time on every machine should not be a different
       /etc/init.d/luci restart
           # Check:
         https://node2.example.com:8084/cluster#
           # Follow the steps to create a cluster
           # Clear memory cache after setup
        echo 3 > /proc/sys/vm/drop_caches
           # Follow the steps to create a cluster and services...
```

# Have Fun with nu11secur1ty =)










     



