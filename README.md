# Linux_Task_1
### Part 1: LVM
* Create a volume group, and set 16M as extends. And divided a volume group containing 50 extends on
volume group lv, make it as ext4 file system, and mounted automatically under /mnt/data. Please
note that this should be implemented on the second disk

1. First, I added a new disk manually, then i created 2 partitions.
2. Create a physical volume for each partition
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

 James Madison
2. James Monroe
3. John Quincy Adams

### Part 2: Users, Groups and Permissions

### Part 3: SSH

### Part 4: Permissions

### Part 5: Permissions

### Part 6: Bash script and processes

### Part 7: Yum Repo

### Part 8: Network management

### Part 9: Cronjob

### Part 10: Mariadb
