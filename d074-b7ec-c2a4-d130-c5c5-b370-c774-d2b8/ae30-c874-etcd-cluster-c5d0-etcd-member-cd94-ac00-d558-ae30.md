# Add etcd member to existing etcd cluster

Etcd cluster가 가동중인 상태에서 etcd member를 추가하는 방법에 대해 기술한다.

* etcd 인증서 디렉토리: /etc/kubernets/pki
* etcd 설정파일: /etc/etcd/etcd.conf

**1.etcd ca 파일을 기반으로 추가하고자 하는 etcd node에서 etcd 인증서를 생성한다.**

```
# ssh로 추가할 etcd node 접속

# cd /etc/kubernetes/pki
# scp root@{ip}:/etc/kubernetes/pki/etcd-ca.* .

# openssl genrsa -out /etc/kubernetes/pki/etcd.key 2048
# openssl req -new -key /etc/kubernetes/pki/etcd.key -subj '/CN=etcd' \
| openssl x509 -req -CA /etc/kubernetes/pki/etcd-ca.crt -CAkey /etc/kubernetes/pki/etcd-ca.key \
-CAcreateserial -out /etc/kubernetes/pki/new/etcd.crt -days 3650 -extensions \
v3_req -extfile /etc/kubernetes/pki/openssl.conf

# openssl genrsa -out /etc/kubernetes/pki/etcd-peer.key
# openssl req -new -key /etc/kubernetes/pki/etcd-peer.key -subj '/CN=etcd-peer' \
| openssl x509 -req -CA /etc/kubernetes/pki/etcd-ca.crt -CAkey /etc/kubernetes/pki/etcd-ca.key \
-CAcreateserial -out /etc/kubernetes/pki/etcd-peer.crt -days 3650 -extensions \
v3_req -extfile /etc/kubernetes/pki/openssl.conf

```

**2.인증서를 재 생성한다.**

이전에 설치를 진행한 디렉토리에서 cert.yaml을 실행하여 인증서를 update한다.

먼저, openssl.conf에서 추가할 ip나 dns를 IP.N에 추가 기입한다.

```
# vi cubescripts/roles/sslcert/openssl.conf.j2
[req]
req_extensions = v3_req
distinguished_name = req_distinguished_name
[req_distinguished_name]
[ v3_req ]
basicConstraints = CA:FALSE
keyUsage = nonRepudiation, digitalSignature, keyEncipherment
subjectAltName = @alt_names
[alt_names]
DNS.1 = kubernetes
DNS.2 = kubernetes.default
DNS.3 = kubernetes.default.svc
DNS.4 = kubernetes.default.svc.{{ cluster_name }}
DNS.5 = {{ domain_name }}
DNS.6 = *.{{ domain_name }}
DNS.7 = localhost
{% if cloud_provider == 'azure' or cloud_provider == 'aws' -%}
DNS.8 = {{ lb_ip }}
{% endif -%}
IP.1 = 127.0.0.1
IP.2 = {{ kubernetes_service_ip }}
{% if cloud_provider == 'baremetal' or cloud_provider == 'rovius' or cloud_provider == 'virtualbox' -%}
IP.3 = {{ lb_ip }}
IP.4 = 14.52.93.202
{% endif -%}


# ansible-playbook -i inventories/inventory -u {userId} cert.yaml
```

**3.k8s에 생성된 default token를 모두 삭제한다.**

```
# kubectl get secrets --all-namespaces | grep default-token | awk '{print $1, $2}' | xargs -n 2 kubectl delete secrets -n
```

**4. Worker node -&gt; Master Node 순으로 rebooting한다.**

```
# reboot
```



