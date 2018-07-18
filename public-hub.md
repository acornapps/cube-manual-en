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

Harbor 설정파일에서 아래  속성 확인 및 변경하기.

```
# vi harbor/harbor.cfg
...
hostname = 서버 ip
ui_url_protocol = https
db_password = root123
ssl_cert = /root/cocktail/cert/harbor.crt     (공인인증서 crt 파일 경로)
ssl_cert_key = /root/cocktail/cert/harbor.key (공인인증서 key 파일 경로)
harbor_admin_password = xxxxx
...
```

* Harbor 설치 및 기동

./prepare 스크립트를 실행해서 harbor 설정 파일들을 generation한다.

Harbor가 이미 떠 있는 경우에는 stop한 후 start 한다.

```
# ./prepare
# docker-compose down
# docker-compose up -d
```

* 참고 - Harbor 실행/중단/설정변경시

Harbor는 부팅시에 자동 실행되며 수동으로 기동, 종료 및 설정 변경시에 아래 명령을 실행하면 된다.

```
# cd cocktail/harbor
# docker-compose start
# docker-compose stop

설정을 변경한 경우에는 install.sh 파일을 다시 실행한다.
```

* ---

  Docker Build Server 설치.

Docker build Server는 docker상에 container로 실행되며 Cocktail Builder-api component와 통신하게 된다.

또한,  Docker Server와 Docker Client간 로컬이 아닌 원격지에서는 반드시 TLS로 통신해야 함으로 사설인증서를 생성하여 설정해 준다.

이를 편하게 설정하기 위해 "create-docker-tls.sh" 파일을 설치하고자 하는 VM 또는 machine에 upload하여 실행한다.

```
# ./create-docker-tls.sh "도메인 또는 IP"
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

* Cocktail에서 builder-api, client-api deployment update

k8s dashboard에서 builder-api의 환경변수 REGISTRY_URL, DOCKER\_URL, SERVER\_TYPE, CA\_PEM, CERT\_PEM, KEY\_PEM 를 수정하고, cocktail-api의 환경변수 REGISTRY\_URL을 수정한다._

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



