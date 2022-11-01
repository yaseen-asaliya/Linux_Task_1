# Linux_Task_1
***Part 1:*** <br>
Create a volume group, and set 16M as extends. And divided a volume group containing 50 extends on
volume group lv, make it as ext4 file system, and mounted automatically under /mnt/data. Please
note that this should be implemented on the second disk <br>

1. First, I added a new disk manually, then i created 2 partitions.
2. Create a Physical Volume
<code>
  # pvcreate /dev/sdb1 /dev/sdb2
  > ss
</code>
<br>
- 

<code>
  # vgcreate -s 16M vg1 /dev/sdb1 /dev/sdb2
  # lvcreate -L +50M -n lvm1 /dev/vg1
  # mkfs -t ext4 /dev/vg1/lvm1 
  # mkdir /mnt/data <br>
  # mount /dev/vg1/lvm1 /mnt/data 
  <code>
