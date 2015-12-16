# shareControl
This is collection of scripts to create a samba time machine.

#Folder structure
- `/c` Ready shares dir
- `/shareControl`  Scripts and config
- `/persistent/<share name>` Big persistent storage
- `/persistent/<share name>/states` Share states
- `/persistent/<share name>/changes` Share changes between states
- `/overlay/<share name>+date` Created by script. Small partition to collect changes
- `/overlay/<share name>+date/overlay` Created by script. overlayfs upperDir  
- `/overlay/<share name>+date/ready` Created by script. overlayfs result dir

#How it works:
1. Stop samba
2. rsync all changes to /persistent/<share name>/changes/.sync
3. Copy last persistent state as hard links to new state
4. enumerate all changes at .sync,
  * replace changed files in new state
  * ...
6. Unmount overlay partition
7. Cleanup .sync, remove overlay volume
8. Create new lv overlay partiton
9. Mount
10. Start samba

#Compare with rsnapshot
This works like a rsnapshot, but:
- Need no double space for state compare.
- We can limit maximal day usage, also we can online grow overlay partition
- faster if we have big share(for example 1.5tb), and changes about 15 gb per day
- Need no compare each file of share. Only changed files are proceesed
