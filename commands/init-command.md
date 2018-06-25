# `init` command

init는 Kubernetes와 Cocktail을 설치하려는 플랫폼에 맞는 스크립트, 설정 파일 및 기타 필요 자원을 준비하고 초기화한다.

---

## Usage

cube init \[flags\]

## Flags

1. `--provider, -p`



* Kubernetes와 Cocktail을 설치할 플랫폼의 이름을 지정한다.

* 필수: 필수

* 현재 버전에서 지원하는 provider 목록은 아래와 같다.

| flag 값 | Provider |
| :--- | :--- |
| baremetal | Bare metal machine |
| virtualbox | VirtualBox |
| aws | Amazon Web Service |
| azure | Microsoft Azure |
| gce | Google Compute Engine |



