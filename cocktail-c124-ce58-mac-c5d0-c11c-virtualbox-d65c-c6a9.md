# Cocktail Installation

Cocktail은 cube command line interface를 통해 쉽게 local test 환경, baremetal 서버, public cloud\(aws, azure, gcp\) 서버에 설치할 수 있다. Local test 환경은 windows의 경우 hyperv를 통해 minikube를 활용하며 linux, macos의 경우에는 minikube 뿐만 아니라 virtualbox를 통해 cluster 환경을 시험할 수도 있다.

Cube는 내부적으로 docker를 기반으로한 cubetool과 cube cli로 나뉘며, cubetool은 cube cli가 필요로하는 cube script 다운로드와 public cloud infra생성, k8s 설치, cocktail 설치 역할을 수행하며 cube cli는 cube.yaml\(cube 구성파일\)의 처리 및 cubetool이 필요로 하는 구성파일을생성하고 사용자의 cmd option을 처리하는 역할을 수행한다.

## 사전 준비 사항

설치 전 아래와 같은 프로그램들이 미리 설치 되어 있어야 하며 설치 되어 있지 않을 경우 에러 메시지가 발생한다.

1\) Docker 다운로드 후 설치 \(필수\)

[https://store.docker.com/editions/community/docker-ce-desktop-mac](https://store.docker.com/editions/community/docker-ce-desktop-mac)

2\) Vagrant 다운로드 후 설치 \(virtualbox를 이용한 로컬 테스트 환경 구축시\)

[https://www.vagrantup.com/downloads.html](https://www.vagrantup.com/downloads.html)

3\) Virtualbox 다운로드 \(5.1.x version\) \(virtualbox를 이용한 로컬 테스트 환경 구축시\)

[https://www.virtualbox.org/wiki/Download\_Old\_Builds\_5\_1](https://www.virtualbox.org/wiki/Download_Old_Builds_5_1)

4\) SSH private key & public key 생성 및 유저 등록 \(필수\)

키 생성의 경우 아래의 항목에서 상세하게 확인할 수 있다.

```
# ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/cloud/.ssh/id_rsa): /Desktop/cubetest/id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/cloud/git/cubedeploy/virtualbox/5/id_rsa.
Your public key has been saved in /Users/cloud/git/cubedeploy/virtualbox/5/id_rsa.pub.
The key fingerprint is:
SHA256:liTKyW/l3eU9+mBzyksL0AKpYXRsvsQ793nWJiUgJC0 cloud@Clouds-MacBook-Pro.local
The key's randomart image is:
+---[RSA 2048]----+
|     ....        |
|    . .E.o       |
|     o=o=        |
|   o.oo*.o..     |
|    =.. So... .  |
|     . B oo. + o |
|      o + o.o==o.|
|     .     o=+Bo.|
|            o*=. |
+----[SHA256]-----+


# ssh-add id_rsa (유저등록)
```

5\) kubectl 다운로드 \(옵션\)

[https://kubernetes.io/docs/tasks/tools/install-kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl)

6\) minikube 다운로드 \(k8s minikube를 이용한 로컬 테스트 환경 구축시\)

[https://github.com/kubernetes/minikube/releases](https://github.com/kubernetes/minikube/releases)

#### 



