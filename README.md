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

### Log Back In and Verify the Mapper
The previous command may have logged you out of the Telnet session. After logging back in, it’s now safe to run fsck. However, you’ll first need to identify the correct mapper corresponding to the physical devices.
```
dmsetup table
```

### Now we'll fsck and auto accept all changes
This can take awhile, you can `nohup <cmd> &` if you want to run in the backgroup, then monitor ```nohup.out```
```
fsck.ext4 -yvf -C 0 /dev/mapper/cachedev_0
```

### Reboot
```
reboot
```

### Warning Indicator
Simply run the filesystem check again using the Storage Manager app: this is because the indicator that marks the volume healthy lives in a table that needs to be updated.

