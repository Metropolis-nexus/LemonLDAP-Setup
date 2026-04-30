# System Setup

- Add `/etc/yum.repos.d/nginx.repo`

```
[nginx-mainline]
name=nginx mainline repo
baseurl=http://nginx.org/packages/mainline/centos/10/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true
priority=1
```

- Install NGINX

```bash
dnf install -y nginx
systemctl enable --now nginx
```

- Install EPEL

```bash
subscription-manager repos --enable codeready-builder-for-rhel-10-x86_64-rpms
dnf install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-10.noarch.rpm
```

- Install LemonLDAP-NG (we install this from EPEL, the official repos are signed against DSA keys):

```bash
dnf install -y lemonldap-ng lemonldap-ng-selinux lemonldap-ng-fastcgi-server
```

- Clear default NGINX config:

```bash
rm -f /etc/nginx/conf.d/default.conf
touch /etc/nginx/conf.d/default.conf
chmod 644 /etc/nginx/conf.d/default.conf
chattr +i /etc/nginx/conf.d/default.conf
```

- Set proper hostnames:

```bash
sed -i 's/example\.com/llng.metropolis.nexus/g' /etc/lemonldap-ng/* /var/lib/lemonldap-ng/conf/lmConf-1.json /etc/nginx/conf.d/* /etc/httpd/conf.d/*
sed -i 's/test1/app1/g' /etc/lemonldap-ng/* /var/lib/lemonldap-ng/conf/lmConf-1.json /etc/nginx/conf.d/* /etc/httpd/conf.d/*
sed -i 's/test2/app2/g' /etc/lemonldap-ng/* /var/lib/lemonldap-ng/conf/lmConf-1.json /etc/nginx/conf.d/* /etc/httpd/conf.d/*
sed -i 's#http://#https://#g' /etc/lemonldap-ng/* /var/lib/lemonldap-ng/conf/lmConf-1.json /etc/nginx/conf.d/* /etc/httpd/conf.d/*

systemctl enable --now llng-fastcgi-server
systemctl reload nginx
```
