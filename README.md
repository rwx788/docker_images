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

Test created target with:
`iscsiadm -m discovery -t st -p localhost:3260`
