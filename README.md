# Linux_Task_1
### Part 1: LVM
***Create a volume group, and set 16M as extends. And divided a volume group containing 50 extends on
volume group lv, make it as ext4 file system, and mounted automatically under /mnt/data. Please
note that this should be implemented on the second disk***

* First, I added a new disk manually, then i created 2 partitions.
* Create a physical volume for each partition
```
  # pvcreate /dev/sdb1 /dev/sdb2
```

* Create a volum group with 16M extends
```
  # vgcreate -s 16M vg1 /dev/sdb1 /dev/sdb2
```

* Create Logical Volume with 50M extends from Volum Group
```
  # lvcreate -L +50M -n lvm1 /dev/vg1
```

* Make LV as ext4 file system and mount it to /mnt/data
```
# mkfs -t ext4 /dev/vg1/lvm1 
# mkdir /mnt/data
# mount /dev/vg1/lvm1 /mnt/data 
```

### Part 2: Users, Groups and Permissions
***1- Add user: user1, set uid=601 Password: redhat. The user's login shell should be
non-interactive. (no ssh access to server)
2- Add user1 to group TrainingGroup.
3- Add users: user2, user3. The Additional group of the two users: user2, user3 is the admin
group Password: redhat, user 3 with root permissions***

### Part 3: SSH

### Part 4: Permissions

### Part 5: Permissions

### Part 6: Bash script and processes

### Part 7: Yum Repo

### Part 8: Network management

### Part 9: Cronjob

### Part 10: Mariadb
