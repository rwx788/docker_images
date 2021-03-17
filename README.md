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
