### SVN Setup 

```
yum install -y subversion mod_dav_svn
```


$ cat /etc/httpd/conf.d/subversion.conf
```
LoadModule dav_svn_module     modules/mod_dav_svn.so
LoadModule authz_svn_module   modules/mod_authz_svn.so

<Location /svn>
     DAV svn
     SVNParentPath /var/www/svn
     AuthType Basic
     AuthName "Subversion repositories"
     AuthUserFile /etc/svn-auth-users
     AuthzSVNAccessFile /etc/svn-access-control
     Require valid-user
</Location>
```

#### Users

set password for your users
```
$ htpasswd -c -m /etc/svn-auth-users admn
$ htpasswd -m /etc/svn-auth-users admn
```

prepare access control for webpage

$ cat /etc/svn-access-control
```
[/]
* = r
admn = rw

[mainrepo:/]
user2 = rw
admn = rw

[testrepo:/]
user2 = rw
```

#### repo

```
$ cd /var/www/svn

$ svnadmin create testrepo
$ svnadmin create mainrepo

$ chown -R apache.apache mainrepo/
$ chown -R apache.apache testrepo/
```

$ vi /var/www/svn/*repo/conf/svnserve.conf 
```
anon-access = none
authz-db = authz
```

#### initial import
```
$ mkdir -p /tmp/svn-structure-template/{trunk,branches,tags}
$ svn import -m 'Initial import' /tmp/svn-structure-template/ http://myvirt.pad.lan/svn/testrepo/
```

house clearning
```
$ chkconfig httpd on
$ service httpd restart

$ recycle cache
$ service svnserve start
$ service svnserve stop
```

test repo by accessing it through url
**myvirt.pad.lan** is the hostname/fqdn for the server
http://myvirt.pad.lan/svn/testrepo/

SVN USAGE: http://svnbook.red-bean.com/en/1.7/index.html 

### Windows Usage Notes 
```
- download svn, extract to C:\Program Files
- set path C:\Program Files\Apache-Subversion-1.8.8\bin
- From the Desktop, right-click My Computer and click Properties.
- Click Advanced System Settings link in the left column.
- In the System Properties window under Advanced click the Environment Variables button.
- add this ';C:\Program Files\Apache-Subversion-1.8.8\bin' to path. ';' separates paths
```

#### windows add
open cmd
```
\>svn --username admn -m "add folder" import test/ http://myvirt.pad.lan/svn/testrepo/
Adding         test\sdasfdfsdf

Committed revision 4.
```

add files 
```
\>svn --username admn -m "add Guides folder" import Guides/ http://myvirt.pad.lan/svn/testrepo/
Adding         Guides\VMware.txt
Adding         Guides\create-repo
Adding         Guides\php-on-RHEL6.txt
Adding         Guides\svn-setup

Committed revision 5.
```

this is an example of a delete
```
\>svn --username admn -m "delete sdasfdfsdf file" delete http://myvirt.pad.lan/svn/testrepo/sdasfdfsdf

Committed revision 14.

remove test repo entries 
```

on the server remove test repo
```
rm -rf /var/www/svn/testrepo

remove [testrepo:/] from /etc/svn-access-control
```

### tortoiseSVN

USAGE: http://tortoisesvn.net/docs/release/TortoiseSVN_en/index.html

DOWNLOAD: http://tortoisesvn.net/downloads.html

#### create trunk directory and copy code to trunk
```
right click, create a branch /branches/app-1.0
../../../ completely outside of project create a folder called working copy
checkout branch /branches/app-1.0
assuming you not alone on the project, from the moment you have created your branch, til the moment you actually ready to merge someone has let's say made changes to your trunk, so go into working copy dir, and merge from trunk (revision from branch to HEAD '1-HEAD'). 
now commit working directory to the branch, /branches/app-1.0
on trunk, svn update to make sure you have latest version
go to trunk, merge /branches/app-1.0 to trunk. 
```

![svn](https://dl.dropboxusercontent.com/u/1567633/github/svn/svn.PNG)

### websvn

```
$ yum install websvn
```

$ vi /etc/httpd/conf.d/websvn.conf
```
allow from all
service httpd restart
```

to find server. 
```
$ rpm -ql websvn
$ cd /usr/share/websvn/include
$ vi config.php 
$ config->parentPath('/var/www/svn/');

$ service httpd restart
```

#### AD AUTH to websvn

$ vi /etc/httpd/conf.d/websvn.conf
```
Alias /websvn /var/www/websvn
 
AuthBasicProvider ldap
AuthType Basic
AuthzLDAPAuthoritative off
AuthName "Subversion Repository Web Browsing"
AuthLDAPURL "ldap://ad.domain.example.com:3268/DC=domain,DC=example
s,DC=com?sAMAccountName?sub?(objectClass=*)" NONE
AuthLDAPBindDN "CN=LDAP User,CN=Users,DC=domain,DC=example,DC=com"
AuthLDAPBindPassword SecurePasswd
 
require valid-user
```

![websvn](https://dl.dropboxusercontent.com/u/1567633/github/svn/websvn.PNG)