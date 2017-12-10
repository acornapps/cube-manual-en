# Cocktail Installation

Cocktail은 cube command line interface를 통해 쉽게 local test 환경, baremetal 서버, public cloud\(aws, azure, gcp\) 서버에 설치할 수 있다. Local test 환경은 windows의 경우 hyperv를 통해 minikube를 활용하며 linux, macos의 경우에는 minikube 뿐만 아니라 virtualbox를 통해 cluster 환경을 시험할 수도 있다.

Cube는 내부적으로 docker를 기반으로한 cubetool과 cube cli로 나뉘며, cubetool은 cube cli가 필요로하는 cube script 다운로드와 public cloud infra생성, k8s 설치, cocktail 설치 역할을 수행하며 cube cli는 cube.yaml\(cube 구성파일\)의 처리 및 cubetool이 필요로 하는 구성파일을생성하고 사용자의 cmd option을 처리하는 역할을 수행한다.

### 



