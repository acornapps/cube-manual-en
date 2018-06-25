# Cube Introduction

**Cube**는 command line interface를 통해 쉽게 local test 환경, baremetal 서버, public cloud\(AWS, Azure, GCE\) 서버에 **Cocktail**을 설치할 수 있도록 한다. Local test 환경은 Linux, Mac OS의 경우에서 [VirtualBox](https://www.virtualbox.org/)를 통해 cluster 환경을 시험할 수 있다.

**Cube**는 cubetool과 cube cli로 구성되어 있다. cubetool은 Docker container로 작동하며 cube cli가 필요로하는 cube script 다운로드와 public cloud infra생성, k8s 설치, cocktail 설치 역할을 수행한다. cube cli는 cube.yaml\(cube 구성파일\)의 처리 및 cubetool이 필요로 하는 구성파일을 생성하고 사용자의 명령을 처리하는 역할을 수행한다.

