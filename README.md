# nextcloud構築ログ


## VM install

適宜変更
installは適当に
storageは後で作ってマウント
```
virt-install \
--name ubuntu2004 \
--ram 4096 \
--disk path=/var/lib/libvirt/images/test.img,size=20 \
--vcpus 2 --os-variant ubuntu20.04 \
--network bridge=br0 \
--graphics none \
--console pty,target_type=serial \
--location /var/lib/libvirt/images/iso/ubuntu-20.04.4-live-server-amd64.iso,kernel=casper/vmlinuz,initrd=casper/initrd \
--extra-args 'console=ttyS0,115200n8 serial'
```
## Disk attach

install終了後アタッチ
offline専用

```
qemu-img create /var/lib/libvirt/images/nextcloud-vdb.img 5T
```
## その他VM内設定

```
# internalからのsshのみ許可
ufw allow from 192.168.0.0/16 to any port 22
ufw enable
# cloud-init毎回走らんでええ
touch /etc/cloud/cloud-init.disabled
```

## Domain

googledomain ddns利用
```

```