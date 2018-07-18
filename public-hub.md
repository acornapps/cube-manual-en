# Cocktail Public Hub Server Installation

* Docker Installation

Install Docker first on the virtual or physical machine where Cocktail Public Hub is to be installed.

```
# sudo su - root
# mkdir cocktail        // Creates a working directory.
# yum install –y yum-utils
# yum-config-manager  --add-repo https://download.docker.com/linux/centos/docker-ce.repo
# yum makecache fast
# yum install -y docker-ce
# systemctl enable docker
# systemctl start docker
```

* Docker-Compose 설치

# Docker-Compose Installation Docker-Compose is installed as follows:

    # mkdir -p cocktail
    # cd cocktail
    # curl -L https://github.com/docker/compose/releases/download/1.12.0/docker-compose-`uname -s`-`uname -m` > docker-compose
    # chmod +x docker-compose
    # mv docker-compose /usr/local/bin/
    # docker-compose --version

* Harbor Installation

Download and decompress the Harbor installation file

```
# cd cocktail
# wget https://github.com/vmware/harbor/releases/download/v1.1.1-rc4/harbor-online-installer-v1.1.1-rc4.tgz
# tar -zxvf harbor-online-installer-v1.1.1-rc4.tgz
```

Check and modify the following properties in the Harbor settings file.

```
# vi harbor/harbor.cfg
...
hostname = server ip
ui_url_protocol = https
db_password = root123
ssl_cert = /root/cocktail/cert/harbor.crt     (Digital certificate crt file path)
ssl_cert_key = /root/cocktail/cert/harbor.key (Digital certificate key file path)
harbor_admin_password = xxxxx
...
```

* Harbor Installation and Startup

Run the ./prepare script to generate Harbor settings files.

If Harbor is already running, stop Harbor before starting.

```
# ./prepare
# docker-compose down
# docker-compose up -d
```

* Note: When Running, Stopping, or Reconfiguring Harbor

Harbor runs automatically at boot time. To manually start, stop, or reconfigure Harbor, execute the following command:

```
# cd cocktail/harbor
# docker-compose start
# docker-compose stop

If settings have been changed, run the install.sh file again.
```

* ---

  Docker Build Server Installation.

The Docker build server runs as a container on Docker and communicates with the Cocktail builder-api component.

Remote communication between a Docker server and Docker client must be performed via TLS, and so a private certificate must be generated and set up.

To make this process easier, the "create-docker-tls.sh" file is uploaded to and run on the target virtual or physical machine.

```
# ./create-docker-tls.sh "Domain or IP"
# cd /root/.docker
# ls -al
# vi /etc/profile.d/docker.sh
  export DOCKER_CERT_PATH=/root/home/.docker -> export DOCKER_CERT_PATH=/root/.docker 로 변경

# ./docker.sh
# vi /lib/systemd/system/docker.service  

ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2376 -H unix:///var/run/docker.sock --tlsverify --tlscacert=/root/.docker/ca.pem --tlscert=/root/.docker/server-cert.pem --tlskey=/root/.docker/server-key.pem

# systemctl daemon-reload
# systemctl restart docker
```

* Builder-api and client-api deployment update on Cocktail

Modify the builder-api environment variables (REGISTRY_URL, DOCKER_URL, SERVER_TYPE, CA_PEM, CERT_PEM, and KEY_PEM) and cocktail-api environment variable (REGISTRY_URL) from the k8s dashboard._

| 환경변수 | 값 예시 |
| :--- | :--- |
| REGISTRY\_URL | xxx.xxx.xxx.xxx |
| DOCKER\_URL | [https://xxx.xxx.xxx.xxx](https://172.10.1.1) |
| SERVER\_TYPE | 임의의 string |
| CA\_PEM | build server 인증키 ca.pem파일을 base64 encoding해서  입력 |
| CERT\_PEM | build server cert.pem파일을 base64 encoding해서 입력 |
| KEY\_PEM | build server key.pem파일을 base64 encoding해서 입력 |

| 환경변수 | 값 예시 |
| :--- | :--- |
| REGISTRY\_URL | [https://](https://172.10.1.1)xxx.xxx.xxx.xxx |
| REGISTRY\_USER | harbor login id |
| REGISTRY\_PASSWORD | harbor password |



