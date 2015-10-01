Setup Galaxy on an NFS share for the GVL
========================================

#### For GVL 4.0. GVL Base flavor. Transient storage type.

1. Launch an instance at [launch.genome.edu.au](launch.genome.edu.au) and start
   up the required worker nodes on CloudMan.

2. Email the list of IP addresses assigned to your machines to VicNode support
   (support@vicnode.org.au). Ask them to allow access to the client IP
   addresses for the NFS share.

3. SSH onto the head node and create a temporary mount for the NFS.

```bash
sudo su
mkdir /tmp_nfs
chmod a+w /tmp_nfs
mount -t nfs \
    -o rw,sync,noatime,noacl,intr,rsize=32768,wsize=32768,vers=4 172.XX.XX.XX:/data01 \
    /tmp_nfs
```

4. Copy the files and folders from the galaxy mount to the NFS share preserving
   the user and group permissions.

```bash
su galaxy
mkdir /tmp_nfs/galaxy
mkdir /tmp_nfs/galaxy/tmp
chmod a+wrx /tmp_nfs/galaxy/
chmod a+wrx /tmp_nfs/galaxy/tmp
cp -rp  /mnt/galaxy/c* \
      /mnt/galaxy/files \
      /mnt/galaxy/galaxy-app \
      /mnt/galaxy/object_store_cache \
      /mnt/galaxy/openid_consumer_cache \
      /mnt/galaxy/shed_tools \
      /mnt/galaxy/t* \
      /mnt/galaxy/upload_store \
      /mnt/galaxy/var \
      /mnt/galaxy/whoosh_cache \
      /mnt/galaxy/.python_eggs \
      /tmp_nfs/galaxy
exit

# These files failed:
# cp: failed to preserve ownership for ‘/tmp_nfs/galaxy/galaxy-app/reports.conf.d/400_cloudman_override_proxy_prefix_props.ini’: Permission denied
# cp: failed to preserve ownership for ‘/tmp_nfs/galaxy/galaxy-app/reports.conf.d/400_cloudman_override_app_main_props.ini’: Permission denied
# cp: failed to preserve ownership for /tmp_nfs/galaxy/galaxy-app/reports.conf.d/010_reports_wsgi.ini: Permission denied
# cp: failed to preserve ownership for ‘/tmp_nfs/galaxy/galaxy-app/reports.conf.d’: Permission denied

ls -la /mnt/galaxy/galaxy-app/reports.conf.d
ls -la /tmp_nfs/galaxy/galaxy-app/reports.conf.d
rm -r /tmp_nfs/galaxy/galaxy-app/reports.conf.d
cp -rp /mnt/galaxy/galaxy-app/reports.conf.d /tmp_nfs/galaxy/galaxy-app/
# owner is 'nobody' and not root. Is this okay?

su postgres
cp -rp /mnt/galaxy/db /tmp_nfs/galaxy
exit

su galaxy
cp -rp /mnt/galaxy/gvl /tmp_nfs/galaxy
rm /tmp_nfs/galaxy/gvl/poststart.d/gvl_default_fs_init
exit

cp -rp /mnt/galaxy/gvl/poststart.d/gvl_default_fs_init /tmp_nfs/galaxy/gvl/poststart.d/

umount /tmp_nfs
rmdir /tmp_nfs
exit
```

5. (Skip this step. Manually changing the persistent_data-current.yamly file
   is easier.)
   Login to CloudMan. Go to the admin page. Remove the current 'galaxy' filesystem by clicking on the X.
   Create a new filesystem entry:
    - File system source or device: NFS
    - NFS server address: 172.XX.XX.XX/data01/galaxy
    - File system name: nfs


6. Edit `/mnt/persistent_data-current.yaml` to change the galaxy filesystem to NFS.

   Before:

```
cluster_name: !!python/unicode 'galaxy-mel'
cluster_storage_type: transient
cluster_type: !!python/unicode 'Galaxy'
deployment_version: 2
filesystems:
- kind: nfs
  mount_options: null
  mount_point: !!python/unicode '/mnt/nfs'
  name: !!python/unicode 'nfs'
  nfs_server: !!python/unicode '172.XX.XX.XX/data01/galaxy'
  roles:
  - GenericFS
- kind: transient
  mount_point: /mnt/galaxyIndices
  name: galaxyIndices
  roles:
  - galaxyIndices
machine_image_id: ami-000035ef
persistent_data_version: 3
placement: melbourne-qh2-uom
services:
- name: Supervisor
  roles:
  - Supervisor
- name: Slurmd
  roles:
  - Slurmd
- name: Nginx
  roles:
  - Nginx
- name: Slurmctld
  roles:
  - Slurmctld
  - Job manager
tags: {}
```

    After:

```
cluster_name: !!python/unicode 'galaxy-mel'
cluster_storage_type: transient
cluster_type: !!python/unicode 'Galaxy'
deployment_version: 2
filesystems:
- kind: nfs
  mount_options: null
  mount_point: !!python/unicode '/mnt/galaxy'
  name: !!python/unicode 'galaxy'
  nfs_server: !!python/unicode '172.XX.XX.XX:/data01/galaxy'
  roles:
  - galaxyTools
  - galaxyData
- kind: transient
  mount_point: /mnt/galaxyIndices
  name: galaxyIndices
  roles:
  - galaxyIndices
machine_image_id: ami-000035ef
persistent_data_version: 3
placement: melbourne-qh2-uom
services:
- name: Supervisor
  roles:
  - Supervisor
- name: Slurmd
  roles:
  - Slurmd
- name: Nginx
  roles:
  - Nginx
- name: Slurmctld
  roles:
  - Slurmctld
  - Job manager
tags: {}
```

7. Create galaxy mount point at /mnt/ and chown to galaxy:galaxy

8. `Sudo reboot`
