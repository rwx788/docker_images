## Table of Contents
* [Description](#description)
* [Containers](#containers)
  * [iscsi](#iscsi)
  * [nfs](#nfs)
  * [postgres](#postgres)
  * [samba](#samba-without-netbios)
  * [tinyproxy](#tinyproxy)

# Description
Repository contains docker configuration for different purposes.

# Containers

## iscsi
Runs iscsi with 20GB disk.
This requires running `dd if=/dev/zero of=/opt/iscsi-disk seek=1M bs=20480 count=1` on the host.

To build the image use following command:
```
docker build -t iscsi:latest iscsi/
```

Then, you can run it using following command:
```
docker run --privileged --rm --network=host -v /opt/iscsi-disk:/opt/iscsi-disk --name iscsi iscsi
```
Target name is `iqn.2019-11.iscsi.test:for.test` and is configured in `iscsi/iscsi-target.conf`.

To test with qemu and ibft:
1. Download or build ipxe binary, see https://ipxe.org/download
2. When running qemu point it too ipxe kernel
3. Set as kernel args `dhcp && sanhook iscsi:192.168.122.1::3260:1:iqn.2019-11.iscsi.test:for.test` (e.g. using -append option)


In order to mount disk, run `iscsiadm -m node -T "iqn.2019-11.iscsi.test:for.test" -p "localhost:3260" --login`
After that disk should be visible in the system and can be mounted and formatted as usual.

Test created target with:
`iscsiadm -m discovery -t st -p localhost:3260`

## nfs
Simple nfs share with v3 support and rw share.

Before starting the container, kernel modules should be enabled on the host:
```
modprobe nfs
modprobe nfsd
```

Then build image using following command:
```
docker build -t nfs:latest nfs
```

Start container with:
```
docker run --privileged --rm --network=host --name nfs nfs
```

Due to requirement to have kernel modules enabled, we have to add `--privileged`
option here.

By default, `/export/data` directory will be shared, use `-v` option to map
other directory.


## postgres

PostgreSQL containers available on docker hub do not need any modifications
to be used for testing purposes, see https://hub.docker.com/_/postgres

Database can be started using following command:
```
docker run --name postgres -d -p 5432:5432 -e POSTGRES_PASSWORD=nots3cr3t postgres
```
It will spawn postgres instance with default user (postgres) and password which
is set using `POSTGRES_PASSWORD` environmental variable.


## samba (without netbios)
Simple file sharing over samba protocol without netbios.
See `smb.conf` file for more details.

By default shared directory is called `Shared` and can be accessed by guest user
and is writable. On top there is `smbuser` with password `smbuser`, in case
access with authentication has to be tested.
Container can be build using following command:
```
docker build -t samba:latest samba
```

Define folder to share and forward port 445 when run the container:
```
docker run --name samba -p 445:445 -v /share:/share samba
```

After that you can mount it using following command:
```
mount -t cifs -o username=guest,password="" //192.168.99.1/Shared /mnt/smb
```

On macOS, one can use `mount_smbfs` command:
```
mount_smbfs -N //guest@192.168.99.1/Shared /mnt/smb
```

In case you want to run container with privileged network, it's strongly
recommended to add access control to the `[global]` section of the `smb.cfg`
file, e.g.:
```
hosts allow = 192.168.99.0/24 10.0.0.0/24
hosts deny = 0.0.0.0/0
interfaces = 192.168.99.0/24 10.0.0.0/24
bind interfaces only = yes
```

## tinyproxy
Simple tinyproxy image, without authentication.
In order to enable basic authentication, setup it as follows in tiny.conf:
```
BasicAuth user password
```
Container has port 8888 and allows all connections, which is default behavior
if no `Allow` entries exist in the config file.

Then build image using following command:
```
docker build . --tag tinyproxy:latest
```

Start container with:
```
docker run -p 8888:8888 --name tinyproxy tinyproxy:latest
```
