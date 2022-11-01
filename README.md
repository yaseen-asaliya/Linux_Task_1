# Linux_Task_1
***Part 1:*** <br>
Create a volume group, and set 16M as extends. And divided a volume group containing 50 extends on
volume group lv, make it as ext4 file system, and mounted automatically under /mnt/data. Please
note that this should be implemented on the second disk <br>

* First, I added a new disk manually, then i created 2 partitions.
* Create a Physical Volume for each partition
<code>
  # pvcreate /dev/sdb1 /dev/sdb2
</code>
<br>

*  
<code>
  # vgcreate -s 16M vg1 /dev/sdb1 /dev/sdb2
</code>
<br>
*
<code>
  # lvcreate -L +50M -n lvm1 /dev/vg1
</code>
<br>
*
<code>
  # mkfs -t ext4 /dev/vg1/lvm1 
</code>
<br>
*
<code>
  # mkdir /mnt/data
</code>
<br>
*
<code>
 # mount /dev/vg1/lvm1 /mnt/data 
</code>
