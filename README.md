# glusterfs-config

###Step by step to setup

fsidk -l /dev/sdb
pvcreate /dev/sdb1
vgcreate vg_proxmox /dev/sdb1
lvcreate --name lv_proxmox -l 100%vg vg_proxmox
mkfs -t xfs -f -i size=512 -n size=8192 -L PROXMOX /dev/vg_proxmox/lv_proxmox
mkdir -p /data/proxmox
nano /etc/fstab

/dev/mapper/vg_proxmox-lv_proxmox /data/proxmox xfs defaults 0 0


mount -a
mount | grep proxmox

apt-get install glusterfs-server glusterfs-client
reboot now
systemctl start glusterd


gluster peer probe 10.X.X.X (NODE IP)

gluster volume create gfs-volume-proxmox transport tcp replica 2 10.X.X.X:/data/proxmox 10.X.X.X:/data/proxmox force

gluster volume start gfs-volume-proxmox

gluster volume info

To add more Gluster bricks

gluster volume add-brick gfs-volume-proxmox replica 3 10.X.X.X:/data/proxmox force

Add Gluster storage to Proxmox

In Proxmox web interface select "Datacenter" > "Storage" > "Add" > "GlusterFS"

Enter an ID and two of the three server IP addresses (i.e from above)

Enter the volume name from above (i.e. "gfs-volume-proxmox")

To configure Debian-based systems to automatically start the glusterd service every time the system boots, enter the following from the command line:

# systemctl enable glusterd

