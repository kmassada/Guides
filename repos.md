Repos
=====

setting up a local repository for centos6 RHEL6. This quick guide uses httpd, and file system, as well as adds a cronjob to keep repo up to date

install tools
```
yum install createrepo
yum install yum-utils
```

## Configure httpd 

$ vi /etc/http/httpd.conf
```
<Directory />
    Options Indexes FollowSymLinks Includes ExecCGI
    AllowOverride All
    Order allow,deny
    Allow from all
</Directory>
```

$ vi /etc/httpd/conf.d/repo.conf
```
Alias /repo /nfs/repo

<Directory "/nfs/repo">
   Options Indexes FollowSymLinks MultiViews
   AllowOverride None
   Order allow,deny
   Allow from all
</Directory>
```

restart service
```
$ service httpd restart
```

## Setup Packages

mount iso, copy files
```
wget http://mirror.umd.edu/centos/6/isos/x86_64/CentOS-6.5-x86_64-bin-DVD1.iso
wget http://mirror.umd.edu/centos/6/isos/x86_64/CentOS-6.5-x86_64-bin-DVD2.iso
mkdir -p /mnt/cd /nfs/repo/rhel6/repodata 
mount -o loop CentOS-6.5-x86_64-bin-DVD1.iso  /mnt/cd/
rsync -avHPS /mnt/cd/  /nfs/repo/rhel6
mount -o loop CentOS-6.5-x86_64-bin-DVD2.iso  /mnt/cd/
rsync -avHPS /mnt/cd/  /nfs/repo/rhel6
```
note: I use http://mirror.umd.edu/centos/6/os/x86_64/ because it is the fastest at my location. 

move Packages/*.rpm into /nfs/repo/rhel6
remove all other folders but RPMs Packages and repodata folder
```
cd /nfs/repo/rhel6/
rm -rf CentOS_BuildTag EULA images TRANS.TBL EFI GPL isolinux

mv repodata/*comps.xml repodata/comps.xml
createrepo -g repodata/comps.xml .
```

this is optional, import keys 
```
sudo rpm --import /nfs/repo/rhel6/RPM-GPG-KEY-redhat-beta /nfs/repo/rhel6/RPM-GPG-KEY-redhat-release
```

$ cat /etc/yum.repos.d/rhel-local.repo
```
[rhel6-local]
name=RHEL 6.5 local repository
baseurl=file:///nfs/repo/rhel6/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
enabled=1
```

$ cat /etc/yum.repos.d/rhel-http.repo
```
[rhel6-www]
name=RHEL 6.5 http repository
baseurl=http://192.168.1.10/repo/rhel6/
gpgcheck=1
gpgkey=http://192.168.1.10/repo/rhel6/RPM-GPG-KEY-centos-6
enabled=1
```

tidy up
```
yum clean all
yum repolist
```

## Repo Sync

attempt first sync towards repo
```
reposync --gpgcheck -l --repoid=rhel-x86_64-server-6 --download_path=/opt/rhel6repo
```

script that performs sync with mirror
$ vi /nfs/repo/sync_rhel6repo.sh 
```
cd /nfs/repo/rhel6
rsync  -avSHP --existing --include="Packages/" --include="repodata/" --include="RMP*" --exclude="repodata/comps.xml"    --exclude="repodata/repomd.xml" rsync://mirror.umd.edu/centos/6/os/x86_64/ /nfs/repo/rhel6/
```

logs file required
```
$ mkdir /nfs/repo/logs/
```

$crontab -e
```
* 8 * * * /nfs/repo/sync_rhel6repo.sh >>/nfs/repo/logs/sync_rhel6repo-\`date +%m-%d_[%H,%M]\`.log 2>&1
```
