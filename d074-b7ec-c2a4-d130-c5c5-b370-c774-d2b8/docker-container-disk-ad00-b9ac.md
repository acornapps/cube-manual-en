# Manage Docker container disk

If using docker overlay storage driver, all stdout and stderr data stored to $DOCKERROOT/containers/{containerid}/containerid-json.log and it grow up continuously. To prevent this behavior add logroation configuration file as below: ($DOCKER_ROOT is /data/docker).

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

* Rotation Period: one of daily, weekly, monthly, yearly
* rotate: Log files are rotated count times before being removed or mailed to the address specified in a mail directive. If count is 0, old versions are removed rather than rotated.
* compress : Old versions of log files are compressed with gzip by default.
* copytruncate : Truncate the original log file in place after creating a copy, instead of moving the old log file and optionally creating a new one.
* size : Log files are rotated only if they grow bigger then size bytes. If size is followed by k(kilobytes),M(megabytes), G(gigabytes).

**2. Understanding Docker Storage driver**

Refer to https://www.joinc.co.kr/w/man/12/docker/storage



