# nextcloud on kvm

## install KVM

```
apt install -y qemu qemu-kvm libvirt-daemon libvirt-clients bridge-utils virt-manager
reboot

```

## VM install

適宜変更
installは適当に
storageは後で作ってマウント
```
--disk path=/var/lib/libvirt/images/test.img,size=20 \
--location /var/lib/libvirt/images/iso/ubuntu-20.04.4-live-server-amd64.iso,kernel=casper/vmlinuz,initrd=casper/initrd \

--console pty,target_type=serial \
```
```
virt-install \
--name ubuntu2004 \
--ram 4096 \
--vcpus 2 --os-variant ubuntu20.04 \
--network bridge=br0 \
--graphics none \
--file /var/lib/libvirt/images/nextcloud.img \
--cdrom /var/lib/libvirt/images/userdata.iso \
--extra-args 'console=ttyS0,115200n8 serial'
```
## Disk attach

install終了後アタッチ
offline専用
```
qemu-img create /var/lib/libvirt/images/nextcloud-vdb.img 5T
virsh attach-disk --domain nextcloud --source /var/lib/libvirt/images/nextcloud-vdb.img --target vdb  --config
```
VM内でマウント,uuid使いましょうね
```
mkfs.ext4 /dev/vdb
vim /ets/fstab
# /dev/disk/by-uuid/d0dd18e9-b25d-4c72-aa97-340c48036836  /data   ext4    defaults        0       0
mkdir /data
mount -a
```

## その他VM内設定

sshd_configでpasswordとrootloginは無効に
```
# とりまinternalからのsshのみ許可　80,443は後ででいいや
ufw allow from 192.168.0.0/16 to any port 22
ufw enable
# cloud-init毎回走らんでええ
touch /etc/cloud/cloud-init.disabled
```

## 問題点
自宅からアクセスするとinternal->globalできなくてダメ（NATループバック・ヘアピンNAT）
->リバースプロキシ構築(ConoHa)

## datadir
外付けしか使えん。`/media`以下にdatadir作成して下記。
- https://askubuntu.com/questions/882625/nextcloud-snap-with-data-directory-on-external-harddrive
```
snap stop nextcloud
snap connect nextcloud:removable-media
vim /var/snap/nextcloud/current/nextcloud/config/config.php
mv /var/snap/nextcloud/common/nextcloud/data /media/nextcloud/data
snap start nextcloud
```



