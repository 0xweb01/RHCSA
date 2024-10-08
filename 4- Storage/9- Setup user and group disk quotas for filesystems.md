

install quota

```bash
dnf install quota
```

________________________________________________________________________________________________

# Enable Quota:

## `nano /etc/fstab`

change `/etc/fstab` to enable the quota (`usrquota` , `grpquota`)


```bash
nano /etc/fstab

/dev/sdb1 /mybackups     xfs    defaults,usrquota,grpquota  0 2
```

and then reboot


```bash
systemctl reboot 
```

________________________________________________________________________________________________


## enforce quota on `ext4` filesystem needs 2 more commands:



### 1- this will create 2 files on the filesystem: `aquata.group` and `aquata.user`

in these files, the system `keeps` the `track` of how much data the user or group is using


## `quotacheck -cug /dev/sdb1`

 (`-cug` = `--create-files --user --group`)

```bash
quotacheck -cug /dev/sdb1
```
 
 OR
 
 
## `quotacheck --create-files --user --group /dev/sdb1`

```bash
quotacheck --create-files --user --group /dev/sdb1
```




________________________________________________________________________________________________

### 2- turn on quata


## `quotaon /mybackups`

```bash
quotaon /mybackups
```

________________________________________________________________________________________________

# Test Quota:

create a file with 100M size:

## `fallocate --length 100M`

allocate 100M to a user


```bash
fallocate --length 100M /mybackups/hamid/100Mfile
```

________________________________________________________________________________________________


## `edquota --user`

### edit the quata of a user

```bash
edquota --user hamid
```


blocks: the allocated size: 102400 --> 100M

1 block = 1 KiloByte

soft limits / hard limits

0 = no limits


each directory/file = 1 inode

to limite the number of files/directories, use inode

________________________________________________________________________________________________


## `edquota --user hamid`

### to Edit the quata of a user


```bash
edquota --user hamid
```


________________________________________________________________________________________________


## `quota --user hamid`

### to Verify the quata of a user


```bash
quota --user hamid
```

grace time : when we pass the soft limit, we have time for deleting the extra data / or we will de blocked from any additional change


________________________________________________________________________________________________


## `quota --edit-period`

### to Edit the quata of a group

```bash
quota --edit-period
```

________________________________________________________________________________________________


## `edquota --group`

edit the quota of a group:

```bash
edquota --group adm
```

________________________________________________________________________________________________


## `quota --group`

### to Verify the quata of a user

```bash
quota --group adm
```
________________________________________________________________________________________________
________________________________________________________________________________________________

## `xfs_quota -x -c`

Edit disk quotas for the user called john. Set a soft limit of 100 megabytes and hard limit of 500 megabytes on /mnt partition.

```bash
[bob@centos-host ~]$ sudo xfs_quota -x -c 'limit bsoft=100m bhard=500m john' /mnt/
```
