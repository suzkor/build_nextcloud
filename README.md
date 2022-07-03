# Nextcloud

## その他

### Domain

googledomain ddns利用
https://techblog.nullstack.engineer/entry/google_domains-ddns_setting/
```
apt install ddclient
cat <<EOF > /etc/ddclient.conf
ssl=yes
use=web
protocol=googledomains
login={generated-username}
password={generated-password}
nextcloud.gongtree.net
EOF
ddclient -daemon=0 -verbose
vi /etc/default/ddclient
# run_ipup="false"
# run_daemon="true"
systemctl restart ddclient.service
systemctl enable ddclient.service
```
