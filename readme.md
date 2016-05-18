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
