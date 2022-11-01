# Linux_Task_1
## Part 1: LVM
_Create a volume group, and set 16M as extends. And divided a volume group containing 50 extends on
volume group lv, make it as ext4 file system, and mounted automatically under /mnt/data. Please
note that this should be implemented on the second disk._
<br><br>
***Solution:***
* The first step is to add a new disk manually to the VM, then create 2 partitions inside disk.
* Create a physical volume for each partition
```
  # pvcreate /dev/sdb1 /dev/sdb2
```

* Create a volum group with 16M extends
```
  # vgcreate -s 16M vg1 /dev/sdb1 /dev/sdb2
```

* Create Logical Volume 50M extends from Volum Group
```
  # lvcreate -L +50M -n lvm1 /dev/vg1
```

* Make LV as ext4 file system and mount it to `/mnt/data`
```
# mkfs -t ext4 /dev/vg1/lvm1 
# mkdir /mnt/data
# mount /dev/vg1/lvm1 /mnt/data 
```







## Part 2: Users, Groups and Permissions

1. Add user: user1, set uid=601 Password: redhat. The user's login shell should be non-interactive. (no ssh access to server).
2. Add user1 to group TrainingGroup.
3. Add users: user2, user3. The Additional group of the two users: user2, user3 is the admin group Password: redhat, user 3 with root permissions.
<br><br>
***Solution:***
* Create non-interactive user (user1) with `uid=601` and `password=redhat`
```
# adduser user1 -s /sbin/nologin
# usermod -u 601 user1
# passwd user1
```
* Add user1 to `TrainingGroup` group
```
# addgroup TrainingGroup
# usermod -g TrainingGroup user1
```

* Create user2 and user3, and set password for them, and set them in admin group
```
# adduser user2
# adduser user3
```
* Set password for `user2` and `user3`
```
# passwd user2
# passwd user3
```
* Create and set both users in `admin` group
```
# groupadd admin
# usermod -g admin user2
# usermod -g admin user3
```

* Set root permissions for user3
```
#  usermod -G root user3
```






## Part 3: SSH
__Generate SSH key and connect to different VM without password.__
<br><br>
***Solution:***
* Genarate `SSH` key and store it in `/root/.ssh`
```
ssh-keygen -t rsa
```
* Copy the key to the destination system (other VM)
```
# cd /root/.ssh
# ssh-copy-id -i id_rsa.pub root@127.0.0.20
```
* To connect with other VM using `SSH` key
```
# ssh root@127.0.0.20
```





## Part 4: Permissions
__Copy /etc/fstab to /var/tmp name admin, the user1 could read, write and modify it, while user2 can’t do
any permission.__
<br><br>
***Solution:***
* Copy file from `/etc/fstab` to `/var/tmp` named admin
```
# cp /etc/fstab /var/tmp/admin
```
* Set read, write, and modify permissions for `user1`
```
# setfacl -m u:user1:rw- /var/tmp/admin
```
* Remove all permissions for `user2`
```
# setfacl -m u:user2:--- /var/tmp/admin
```




## Part 5: Permissions
__SELinux must be running in the Enforcing mode (permanent even after reboot).__
<br><br>
***Solution:***






## Part 6: Bash script and processes
__Write a shell script that will keep running for 10 mins in the background and check the process that it's
created and try to kill using commands.__
<br><br>
***Solution:***
* Create scripts file
```
# vi /var/tmp/scripts.sh
```
* Insert commands in file to keep running in background for 10min

```
#!/bin/bash
echo "scripts start execution"
sleep 10m &
echo "working...."
```
* Make file starts running
```
# chmod +x /var/tmp/scripts.sh
# ./var/tmp
```
* View all active process (to get process id)
```
# ps
```
* to kill the process `(pid=6483)`
```
# kill -15 6483 
```





## Part 7: Yum Repo

## Part 8: Network management




## Part 9: Cronjob
__Create a cronjob that will run at 1:30 AM every day and collect the users logged in and save them in a file.<br>
Format : timestamp – users__
<br><br>
***Solution:***
* Open cronjob file
```
crontab -e
```
* Insert command inside cronjob file to execute 'myscript.sh` file every day at 1:30 AM
```
30 01 * * * /tmp/myscript.sh
```
* Create and open 'myscript.sh` file
```
# vi /tmp/myscript.sh
```
* Insert code to get all logged in users and current timestamp and save the informatio inside `activeusers` file
```
echo "Time: $(date) - Active Users: $(who -q)" >> /tmp/activeusers 2> /tmp/error
echo " " >> /tmp/activeusers
```



## Part 10: Mariadb
