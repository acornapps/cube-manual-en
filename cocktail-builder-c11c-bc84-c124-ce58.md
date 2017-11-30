# Cocktail Builder 서버 설치

MacOS에서 VirtualBox를 이용하여 Cocktail이 제공하는 기능을 간단하게 확인하고자 할 경우에 아래의 절차를 따라 설치한다.

1.Docker를 설치한다. Centos 기준임.

```
# sudo su -
# yum install -y yum-utils
# yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
# yum makecache fast
# yum install docker-ce
# systemctl start docker
```

2.Docker-Compose 설치

```
# sudo su -
# yum install -y yum-uti
# udo su -
```

# mkdir software

# cd software

# curl -L [https://github.com/docker/compose/releases/download/1.12.0/docker-compose-\`uname](https://github.com/docker/compose/releases/download/1.12.0/docker-compose-`uname) -s-uname -m\` &gt; docker-compose

# chmod +x docker-compose

# mv docker-compose /usr/local/bin/

# docker-compose --version

1. Harbor 설치

```
# sudo su -
# cd ~/software
# wget https://github.com/vmware/harbor/releases/download/v1.1.1-rc4/harbor-online-installer-v1.1.1-rc4.tgz
# tar -xvf harbor-online-installer-v1.1.1-rc4.tgz
# cd harbor

# vi harbor.cfg

## Configuration file of Harbor

#The IP address or hostname to access admin UI and registry service.
#DO NOT use localhost or 127.0.0.1, because Harbor needs to be accessed by external clients.
hostname = cube-hub.acornsoft.io

#The protocol for accessing the UI and token/notification service, by default it is http.
#It can be set to https if ssl is enabled on nginx.
ui_url_protocol = https

#The password for the root user of mysql db, change this before any production use.
db_password = root123

#Maximum number of job workers in job service
max_job_workers = 3

#Determine whether or not to generate certificate for the registry's token.
#If the value is on, the prepare script creates new root cert and private key
#for generating token to access the registry. If the value is off the default key/cert will be used.
#This flag also controls the creation of the notary signer's cert.
customize_crt = on

#The path of cert and key files for nginx, they are applied only the protocol is set to https
ssl_cert = /data/cert/star_acornsoft_io.crt
ssl_cert_key = /data/cert/star_acornsoft_io.key

#The path of secretkey storage
secretkey_path = /data

#Admiral's url, comment this attribute, or set its value to NA when Harbor is standalone
admiral_url = NA

#NOTES: The properties between BEGIN INITIAL PROPERTIES and END INITIAL PROPERTIES
#only take effect in the first boot, the subsequent changes of these properties
#should be performed on web ui

#************************BEGIN INITIAL PROPERTIES************************

#Email account settings for sending out password resetting emails.

#Email server uses the given username and password to authenticate on TLS connections to host and act as identity.
#Identity left blank to act as username.
email_identity =

email_server = smtp.mydomain.com
email_server_port = 25
email_username = sample_admin@mydomain.com
email_password = abc
email_from = admin <sample_admin@mydomain.com>
email_ssl = false

##The initial password of Harbor admin, only works for the first time when Harbor starts.
#It has no effect after the first launch of Harbor.
#Change the admin password from UI after launching Harbor.
harbor_admin_password = C0ckt@1lAdmin

##By default the auth mode is db_auth, i.e. the credentials are stored in a local database.
#Set it to ldap_auth if you want to verify a user's credentials against an LDAP server.
auth_mode = db_auth

#The url for an ldap endpoint.
ldap_url = ldaps://ldap.mydomain.com

#A user's DN who has the permission to search the LDAP/AD server.
#If your LDAP/AD server does not support anonymous search, you should configure this DN and ldap_search_pwd.
#ldap_searchdn = uid=searchuser,ou=people,dc=mydomain,dc=com

#the password of the ldap_searchdn
#ldap_search_pwd = password

#The base DN from which to look up a user in LDAP/AD
ldap_basedn = ou=people,dc=mydomain,dc=com

#Search filter for LDAP/AD, make sure the syntax of the filter is correct.
#ldap_filter = (objectClass=person)

# The attribute used in a search to match a user, it could be uid, cn, email, sAMAccountName or other attributes depending on your LDAP/AD
ldap_uid = uid

#the scope to search for users, 1-LDAP_SCOPE_BASE, 2-LDAP_SCOPE_ONELEVEL, 3-LDAP_SCOPE_SUBTREE
ldap_scope = 3

#Timeout (in seconds)  when connecting to an LDAP Server. The default value (and most reasonable) is 5 seconds.
ldap_timeout = 5

#Turn on or off the self-registration feature
self_registration = on

#The expiration time (in minute) of token created by token service, default is 30 minutes
token_expiration = 30

#The flag to control what users have permission to create projects
#The default value "everyone" allows everyone to creates a project.
#Set to "adminonly" so that only admin user can create project.
project_creation_restriction = everyone

#Determine whether the job service should verify the ssl cert when it connects to a remote registry.
#Set this flag to off when the remote registry uses a self-signed or untrusted certificate.
verify_remote_cert = on
#************************END INITIAL PROPERTIES************************
#############
```



| **프로바이더** | **설명** |
| :--- | :--- |
| Amazon Web Service | Amazon사의 public 클라우드 서비스 |
| Google Cloud Platform | Google사의 public클라우스 서비스 |
| Microsoft cloud Service | Microsoft사의 public 클라우드 서비스 |
| Rovius Cloud | Rovius사의 private 클라우드 서비스 |
| Onpremise | Baremetal환경의 private 클라우드 서비스 |

| **유형** | **설명** |
| :--- | :--- |
| User | 프로바이더 등록을 위한 기본 값. 미터링 아닌 경우 User 선택 |
| Metering | Public Cloud를 사용시, 리소스 사용량을 받아오고자 할 때 선택 |





