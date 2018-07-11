# Docker container disk 관리

Container 내에서 stdout, stderr로 출력을 보내면 docker overlay storage driver를 사용하는 경우 $DOCKERROOT/containers/{containerid}/containerid-json.log 파일에 저장되며 별다른 조치를 하지 않을 경우 계속 증가하게 된다. 이를 방지하기 위해 아래와 해당 디렉토리에 logrotation 설정파일을 추가한다. 아래 /data/docker 디렉토리가  $DOCKER\_ROOT임.

**1./etc/logrotate.d/docker-container**

```
# Added by cube
/data/docker/containers/*/*.log {
  daily
  rotate 4
  compress
  size=10M
  missingok
  delaycompress
  copytruncate
}
```

* 로테이트주기로 **daily, weekly, monthly, yearly **를 선택할 수 있다.
* rotate: rotate되는 파일을 최대 4개까지만 유지하고 이전 파일을 삭제한다.
* compress : 백업파일을 gzip 압축한다. 압축하고 싶지 않다면 nocompress를 설정하면 된다.
* copytruncate : 대부분의 잘 만든 애플리케이션들은 로그파일을 새로 만들기 위한 시그널 처리 코드를 가지고 있다. 하지만 그렇지 않은 애플리케이션들도 있다. copytruncate를 이용하면 원본파일을 지우지 않고 **truncate\(파일 크기를 0으로 만든다\) **한다. 애플리케이션은 파일을 잃어버리지 않으므로 계속 해서 로그를 쓸 수 있다. 다만 파일을 복사하고 truncate 하는 순간 로그를 잃어버릴 수도 있다.
* size : size 보다 로그파일이 클 경우 로테이션을 수행한다. M\(메가\), K\(키로\)단위로 설정 할 수 있다.

2**. Docker Storage driver 구조.**

https://www.joinc.co.kr/w/man/12/docker/storage 참조. 



