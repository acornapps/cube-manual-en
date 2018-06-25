# Kubernetes cluster version upgrade

기 설치된 Cocktail이 사용하는 Kubernetes version을 upgrade하기 위해서 아래의 절차대로 수행한다.

Upgrade할 version은 기존 version의 상위버전으로만 허용되며 하위버전으로의 downgrade는 불가하다. 또한, 현재 1.8.13이상, 1.9.x, 1.10.x 로 지정이 가능하다.

```
# cube upgrade -v version
```

Upgrade가 정상 완료되었는지 확인하기 위해 cube status 명령으로 node의 버전을 조회한다.

```
# cube status
```



