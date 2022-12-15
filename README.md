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
```
# usermod -a -G wheel user3
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
* Install tmux, httpd, and mysql and start apache server
```
#  yum install tmux
#  yum install httpd
# systemctl start httpd
#  yum install mysql
```
* Install helpful packages to create repos
```
# yum install createrepo 
# yum install yum-utils
```
* Create local repository
> Solution (1)
```
mkdir /var/www/html/localrepo
cd /var/www/html/localrepo
wget https://repo.zabbix.com/zabbix/4.4/rhel/7/x86_64/
yum install http://repo.zabbix.com/zabbix/3.2/rhel/6/x86_64/zabbix-release-3.2-1.el6.noarch.rpm
createrepo /var/www/html/localrepo

nano /etc/yum.repos.d/localrepo.repo
**
[localrepo]
name=Apache
baseurl=file:/var/www/html/localrepo
enabled=1
gpgcheck=0
** 

yum-config-manager --disable \*
yum-config-manager --enable localrepo

# yum install zabbix-server-mysql
# yum install zabbix-web-mysql
# yum install zabbix-agent
# yum install php 
```
> Solution (2)
> Create new direcory
```
# mkdir /var/www/html/repos/
# mkdir –p /var/www/html/repos/{base,centosplus,extras,updates}
```
> Synchronize HTTP Repositories
```
# sudo reposync -g -l -d -m --repoid=base --newest-only --download-metadata --download_path=/var/www/html/repos/

# sudo reposync -g -l -d -m --repoid=centosplus --newest-only --download-metadata --download_path=/var/www/html/repos/

# sudo reposync -g -l -d -m --repoid=extras --newest-only --download-metadata --download_path=/var/www/html/repos/

# sudo reposync -g -l -d -m --repoid=updates --newest-only --download-metadata --download_path=/var/www/html/repos/
```
> Create a new repo
```
# createrepo /var/www/html
```
> Move yum repos to tmp directory 
```
# mv /etc/yum.repos.d/*.repo /tmp/
```
* Go to `nano /etc/yum.repos.d/remote.repo` and write the below commands
```
[remote]
name=RHEL Apache
baseurl=http://127.0.0.10
enabled=1
gpgcheck=0
```
* Disable all repository except the new one
```
# yum-config-manager --disable \*
# yum-config-manager --enable remote
```
* Setup Zabbix
```
# yum remove zabbix-release
# yum install http://repo.zabbix.com/zabbix/3.2/rhel/7/x86_64/zabbix-release-3.2-1.el7.noarch.rpm
# yum clean all
```
* Setup Zabbix rpms on new repository
```
# yum --enablerepo=localrepo install zabbix-server-mysql
# yum --enablerepo=localrepo install zabbix-web-mysql
# yum --enablerepo=localrepo install zabbix-agent
# yum --enablerepo=localrepo install php
```

## Part 8: Network management
1. Open port 443 , 80
2. Make the changes permanent
3. Block ssh connection for your colleague ip to the VM.
<br><br>
> Solution 1 (using firewall):
* Open port 80 permanent
```
# firewall-cmd --zone=public --add-port=80/tcp --permanent
```
* Open port 443 permanent
```
# firewall-cmd --zone=public --add-port=443/tcp --permanent
```
* Block ssh connection for client with `ip=10.0.2.11`
```
# firewall-cmd --permanent --add-rich-rule="rule family='ipv4' source address='10.0.2.11' reject"
# firewall-cmd --reload
```
> Solution 2 (using Iptables)
* Open port 80
```
# iptables -I INPUT -p tcp --dport 80 -j ACCEPT
```
* Open port 443
```
# iptables -I INPUT -p tcp --dport 443 -j ACCEPT
```
* Save iptable configuration to `/etc/sysconfig/iptables`
```
# iptables-save > /etc/sysconfig/iptables
```
* After reboot u can restore iptables configurations using 
```
# iptables-restore < /etc/sysconfig/iptables
```


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
* install mariadb and create file to set it's configrations
```
# yum install mariadb-server
# touch /etc/yum.repos.d/MariaDB.repo
```
> inside the new file created write these commands
```
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.3/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
```
> Set another configrations and start mariadb
```
# yum install MariaDB-server MariaDB-client
# systemctl enable mariadb
# systemctl status mariadb
```
> Set new password for connection (NOTE:Answer [y] yes for all choices)
```
# mysql_secure_installation
```
> To login in mariadb (user the password that we created previously)
```
# mysql -u root -p
```
* Open port from the mariadb to be accessible from clients
> Create a new remotely user with 2nd VM ip with permissions
```
MariaDB> GRANT ALL on *.* TO root@'10.0.2.11' IDENTIFIED BY 'osboxes.org';
```
> Flush prevlileges
```
MariaDB> FLUSH PRIVILEGES;
```
> Open the mariadb port using `iptables`
```
# iptables -I INPUT -p tcp --dport 3306 -j ACCEPT
# iptables-save > /etc/sysconfig/iptables
```
> Open the mariadb port using `firewall`
```
# sudo firewall-cmd --permanent --add-port=3306/tcp
# sudo firewall-cmd --reload
```
* Log in in client to server with ip `10.0.2.10`
```
 mysql -h 10.0.2.10 -u root -p
```
* Create database
```
MariaDB> CREATE DATABASE studentdb;
```
* Create require records
> Use database
```
MariaDB> use studentdb
```
> Create table to stored records
```
MariaDB> create table information (
	student_number varchar(255) not null,
	student_first_name varchar(255),
	student_last_name varchar(255),
	program_enrolled varchar(255),
	graduation_year int,
  primary key(student_number)
);
```
> Stored data inside table
```
MariaDB> INSERT INTO information
  (student_number, student_first_name,student_last_name,graduation_year,program_enrolled)
VALUES
  ('110-001','Allen','Brown',2017,'mechanical'),
  ('110-002','David','Brown',2017,'mechanical'),
  ('110-003','Mary','Green',2018,'mechanical'),
  ('110-004','Dennis','Green',2018,'electrical'),
  ('110-005','Joseph','Black',2018,'electrical'),
  ('110-006','Dennis','Black',2020,'electrical'),
  ('110-007','Ritchie','Salt',2020,'computer science'),
  ('110-008','Robert','Salt',2020,'computer science'),
  ('110-009','David','Suzuki',2020,'computer science'),
  ('110-010','Mary','Chen',2020,'computer science');
```





