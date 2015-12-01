# Using snapper for yum rollback

* Install RHEL 7.2 with a LVM thin-provisioned "/"
* Create separate volumes for data that shuold exist outside snapshot/rollback, mounted at locations like /home and /var/log
* Install Snapper:
```
yum -y install snapper
```
* Create the snapper configuration:
```
snapper create-config -f "lvm(xfs)" /
```
* Use snapper to run a yum update, automatically creating a snapshot before and after:
```
snapper create --command "yum -y update"
```
* Review the list of available snapshots, noting the number (# column) of the "post" snapshot, and associated "pre" snapshot
```
snapper list
Type   | # | Pre # | Date                            | User | Cleanup | Description | Userdata
-------+---+-------+---------------------------------+------+---------+-------------+---------
single | 0 |       |                                 | root |         | current     |         
pre    | 1 |       | Mon 30 Nov 2015 04:46:39 PM EST | root |         |             |         
post   | 2 | 1     | Mon 30 Nov 2015 04:46:58 PM EST | root |         |             |         
```
* Using a post # of 2 and pre # of 1 as example, review the changes between #2 and #1:
```
snapper diff 1..2
Binary files /.snapshots/1/snapshot/usr/bin/gencat and /.snapshots/2/snapshot/usr/bin/gencat differ
Binary files /.snapshots/1/snapshot/usr/bin/getconf and /.snapshots/2/snapshot/usr/bin/getconf differ
Binary files /.snapshots/1/snapshot/usr/bin/getent and /.snapshots/2/snapshot/usr/bin/getent differ
Binary files /.snapshots/1/snapshot/usr/bin/iconv and /.snapshots/2/snapshot/usr/bin/iconv differ
Binary files /.snapshots/1/snapshot/usr/bin/locale and /.snapshots/2/snapshot/usr/bin/locale differ
Binary files /.snapshots/1/snapshot/usr/bin/localedef and /.snapshots/2/snapshot/usr/bin/localedef differ
Binary files /.snapshots/1/snapshot/usr/bin/makedb and /.snapshots/2/snapshot/usr/bin/makedb differ
...
```
* Issue a roll-back:
```
snapper undochange 1..2
```
* Issue a roll-forward
```
snapper undochange 2..1
```
* Delete unecessary snapshots to commit changes
```
snapper delete 1
snapper delete 2
```
