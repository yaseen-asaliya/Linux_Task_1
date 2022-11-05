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

* Make LV as ext4 file system 
```
# mkfs -t ext4 /dev/vg1/lvm1 
```
* To mount `/dev/vg1/lvm1` under `/mnt/data`
> Get the id of LV 
```
sudo blkid
```
> Create empty directory
```
# mkdir /mnt/data
```
> go to `nano /etc/fstab` and add this line, then save changes 
>
![image](https://user-images.githubusercontent.com/59315877/200141949-d7a8e417-9674-4207-981c-2e0add49eaf8.png)

> Verify the mounting using below command
```
# df -l
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
> open passwd file
```
#  vi /etc/passwd
```
* Make it as shown below <br>
![image](https://user-images.githubusercontent.com/59315877/199723642-f86e8a39-6477-4571-873d-08c2444489b0.png)






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
* Go to `/etc/selinux/config` file
```
# vi /etc/selinux/config
```
* Change `SELINUX` value to be `SELINUX=enforcing` as shown below <br>
![unnamed](https://user-images.githubusercontent.com/59315877/199683157-11cba0bc-f9e5-43b3-91aa-47f27ffbffc9.png)
* Save the changes, and restart the VM 
```
# reboot
```
* To verify changes use `getenforce` command 
```
# getenforce
```
* The output should be as shown <br>
![ss](https://user-images.githubusercontent.com/59315877/199684315-3149b635-b977-490c-8179-70b5eab94140.png)




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
1. Install tmux on your machine
2. Install apache server on your machine(httpd) and Install mysql.
3. Create a local yum repository on your local machine(available publicly) with the zabbix rpms:
https://repo.zabbix.com/zabbix/4.4/rhel/7/x86_64/
4. Disable all other repositories and keep only the new repo
5. Install zabbix rpms from the new repo (Download zabbix, zabbix-web,php, zabbix-server,
zabbix-agent rpm’s and their dependencies)
<br><br>
***Solution:***





## Part 8: Network management
1. Open port 443 , 80
2. Make the changes permanent
3. Block ssh connection for your colleague ip to the VM.
<br><br>
***Solution:***


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
1. install mariadb from the local repo that was created earlier.
2. open ports in iptables from mariadb.
3. create database , user(note: handle permissions).
4. connect to the database created in step 3 using the new user (with password)
.<br><br>
__Example schema :
Create a MariaDB database called studentdb on rhce1 and add ten records each containing “student
firstname” (Allen, David, Mary, Dennis, Joseph, Dennis, Ritchie, Robert, David, and Mary), “student
lastname” (Brown,Brown, Green, Green, Black, Black, Salt, Salt, Suzuki, and Chen), program enrolled in (3 x
mechanical, 3 x electrical,and 4 x computer science), expected graduation year (2 x 2017, 3 x 2018, 5 x
2020), and a student number (110-001 to 110-010)__
<br><br>
***Solution:***
