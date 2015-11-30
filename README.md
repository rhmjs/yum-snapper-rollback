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
```
* Using a post # of 2 and pre # of 1 as example, issue a roll-back:
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
