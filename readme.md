```bash
wget -P /etc/yum.repos.d http://download.gluster.org/pub/gluster/glusterfs/LATEST/RHEL/glusterfs-epel.repo

yum install glusterfs-server bash-completion
systemctl start glusterd
systemctl enable glusterd

umount /mnt/
vi /etc/fstab
...

cfdisk /dev/vdb
...

pvcreate /dev/vdb1

vgcreate gluster /dev/vdb1

lvcreate --extents 524223 --thin gluster/bricks_thin_pool
lvcreate --virtualsize 1TB --thin gluster/bricks_thin_pool --name brick01

mkfs.xfs -i size=512 /dev/gluster/brick01

gluster volume create kubernetes 192.168.100.134:/mnt/brick01/brick

gluster volume start kubernetes

for i in {1..5}; do ssh kubernetes-node-$i yum install -y glusterfs-fuse; done;
```

##### advantages of using gluster
- *mount storms* alleviated: since a pod might be provisioned with a high replica value, that would mean a high overhead at mount time.
Since `gluster` uses a **scale-out** topology, all the mount request are automatically distributed across `gluster` cluster.
- leverages `gluster` **HA** capability. Again, since `gluster` is a scale out cluster, in case a `gluster` host is unresponsive, `kubelet` goes to the next one to mount the requested volume.
