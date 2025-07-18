# How to properly do a filesystem check (fsck or e2fck) on Synology DSM 7 - Simplified

Steps below were performed on DSM DSM 7.2.2-72806 Update 3. I have a DS724+, and after an improper shutdown, I began experiencing filesystem errors that couldn’t be resolved through the GUI.

## WARNING: Perform these steps at your own risk!

Use Telnet (not SSH) to access your Synology, then elevate to root with sudo:

### Become root
```
sudo -i
```

### Disable all services and unmount volume: This will log out!
```
synospace --stop-all-spaces
```

### Next, go to the DSM GUI and Restart your synology.  
After it comes back online your volume should be unmounted.  Give it a few minutes to finish starting up (5 minutes after ssh is up) and run the commands above to make sure you are not mounted and lvm sees your lvm.  We should be good to fsck now.  
Run this command to see your mapper path to fsck (this step is usually unnecessary if the mapper is cachedev_0, in that case you can go directly to the next command):
```
dmsetup table
```

### Now we'll fsck and auto accept all changes
This can take awhile, you can `nohup <cmd> &` if you want to run in the backgroup, then monitor ```nohup.out```
```
fsck.ext4 -yvf -C 0 /dev/mapper/cachedev_0

e2fsck 1.44.1 (24-Mar-2018)
Pass 1: Checking inodes, blocks, and sizes
Pass 2: Checking directory structure
Pass 3: Checking directory connectivity
Pass 4: Checking reference counts
Pass 5: Checking group summary information

     2325930 inodes used (0.13%, out of 1799725056)
      187831 non-contiguous files (8.1%)
         857 non-contiguous directories (0.0%)
             # of inodes with ind/dind/tind blocks: 0/0/0
             Extent depth histogram: 2289445/27565/33
 12069212190 blocks used (83.83%, out of 14397792256)
           0 bad blocks
        4518 large files

     1944248 regular files
      372657 directories
           4 character device files
           1 block device file
           0 fifos
       57177 links
        9010 symbolic links (8872 fast symbolic links)
           1 socket
------------
     2383098 files

```

### Reboot
```
reboot
```

### Warning Indicator
Simply run the filesystem check again using the Storage Manager app: this is because the indicator that marks the volume healthy lives in a table that needs to be updated.

