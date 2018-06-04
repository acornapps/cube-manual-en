# Centos yum 자동 upgrade 방지

Cocktail은 k8s package의 버전에 따라 설정파일의 차이가 있으므로 기본적으로 시스템에서 자동적으로 yum upgrade가 설정되어 있으면 security만 업그레이드가 되도록 수정해야 한다. 단, cube script로 설치될 경우에는 자동으로 설정값을 update하게 되어 있다.

* yum-cron 설정 파일 수정.

```
# vi /etc/yum/yum-cron.conf
  ...
  update_cmd = security 로 수정

# vi /etc/yum/yum-cron-hourly.conf
  ...
  update_cmd = security 로 수정
```

참고\) [https://www.lesstif.com/pages/viewpage.action?pageId=46366785](https://www.lesstif.com/pages/viewpage.action?pageId=46366785), [http://webdir.tistory.com/175](http://webdir.tistory.com/175)

