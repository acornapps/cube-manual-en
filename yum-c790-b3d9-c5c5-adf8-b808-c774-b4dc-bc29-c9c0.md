# Automatic CentOS Yum Upgrade Prevention

There are differences in Cocktail settings files depending on the version of a k8s package. If the system is set to automatically upgrade CentOS Yum by default, the settings must be modified to only upgrade the security. If Yum is installed via Cube script, however, the settings are automatically updated.

* Modifying the yum-cron settings file

```
# vi /etc/yum/yum-cron.conf
  ...
  update_cmd = security (modify to)

# vi /etc/yum/yum-cron-hourly.conf
  ...
  update_cmd = security (modify to)
```

참고\) [https://www.lesstif.com/pages/viewpage.action?pageId=46366785](https://www.lesstif.com/pages/viewpage.action?pageId=46366785), [http://webdir.tistory.com/175](http://webdir.tistory.com/175)

