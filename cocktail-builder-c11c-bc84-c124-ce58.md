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

    # sudo su -
    # mkdir software
    # cd software
    # curl -L https://github.com/docker/compose/releases/download/1.12.0/docker-compose-`uname -s`-`uname -m` > docker-compose
    # chmod +x docker-compose
    # mv docker-compose /usr/local/bin/
    # docker-compose --version

3.Harbor 설치

Harbor를 다운로드 받아서 압축을 푼후 설정파일의 값을 아래와 같이 수정한다.

```
# sudo su -
# cd ~/software
# wget https://github.com/vmware/harbor/releases/download/v1.1.1-rc4/harbor-online-installer-v1.1.1-rc4.tgz
# tar -xvf harbor-online-installer-v1.1.1-rc4.tgz
# cd harbor
```

harbor.cfg파일을 vi로 열어서 아래 표와 같이 해당 필드의 값을 수정한다.

| 필드명 | 값 |
| :--- | :--- |
| hostname | cube-hub.acornsoft.io |
| ui\_uri\_protocol | https |
| db\_password | root123 |
| ssl\_cert | /data/cert/star\_acornsoft\_io.crt |
| ssl\_cert\_key | /data/cert/star\_acornsoft\_io.key |
| harbor-admin-password | C0ckt@1lAdmin |

```
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

...
```



