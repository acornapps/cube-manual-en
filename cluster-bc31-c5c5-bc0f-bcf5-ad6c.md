# Cluster backup & restore

This section describes the procedure for reinstalling and restoring an existing k8s cluster state by restoring etcd snapshot, cocktail cmdb, and builderdb.

In such cases, the previous etcd certificate must be used for a successful restoration. Otherwise, the restoration cannot be completed.

* etcd 인증서 디렉토리: /etc/kubernets/pki
* etcd 설정파일: /etc/etcd/etcd.conf

**1.etcd, cocktail cmdb backup**

Modify the shell script according to your own environment as shown below to create an etcd snapshot and back up the cocktail db. Then, perform backups periodically.

    # vi cocktail_backup.sh
    #!/bin/sh

    # usage: cocktail_backup.sh save_path(path for storing backup data) days(storage period in days)
    # ./cocktail-backup.sh /nas/BACKUP/ 10

    export ETCDCTL_API=3

    ETCD_CERT="/etc/kubernetes/pki/etcd-peer.crt"
    ETCD_KEY="/etc/kubernetes/pki/etcd-peer.key"
    ETCD_CACERT="/etc/kubernetes/pki/etcd-ca.crt"

    ETCD_EP="https://192.168.0.202:2379"            // Sets ETCD endpoint.
    CURRENT_DATE=`date '+%Y%m%d'`
    CURRENT_TIME=`date '+%Y%m%d_%H%M%S.db'`

    ETCD_BACKDIR="$1/etcd"
    COCKTAIL_BACKDIR="$1/db"

    error_exit() {
        echo "error: ${1:-"unknown error"}" 1>&2
        exit 1
    }

    verify_prereqs() {
        echo "Verifying Prerequisites"

        if [ ! -d $ETCD_BACKDIR ]; then
            error_exit "Can't access etcd backup directory $ETCD_BACKDIR"
        fi

        if [ ! -d $COCKTAIL_BACKDIR ]; then
            error_exit "Can't access cmdb backup directory $ETCD_BACKDIR"
        fi

        cocktail_cmdb_pod=`kubectl get pods -n cocktail-system | grep cocktail-cmdb | awk '{print $1}'`

        if [ -z $cocktail_cmdb_pod ]; then
            echo "Can't get cocktail cmdb pod name. exit."
            exit 1;
        fi

        cocktail_cmdb_pvc=`kubectl get pvc -n cocktail-system | grep cocktail-cmdb | awk '{print "cocktail-system-"$1"-"$3}'`

        if [ -z $cocktail_cmdb_pvc ]; then
            echo "Can't get somac cmdb pvc name. exit."
            exit 2;
        fi
    }

    main() {
        if [ "$#" -ne 2 ]; then
            error_exit "Illegal number of parameters. You must pass backup directory path and number of days to keep backups"
        fi

        verify_prereqs

        echo "Getting ready to backup to etcd($ETCD_BACKDIR), cmdb($COCKTAIL_BACKDIR)"

        kubectl exec "$somac_cmdb_pod" -n cocktail-system -- sh -c "cd /var/lib/mysql; /usr/bin/mysqldump --single-transaction --databases cocktail builder -u root -pC0ckt@il > somac_cmdb_dump.sql"
        echo "Somac cmdb dump succeeded."

        # etcd backup
        /bin/etcdctl --cert "$ETCD_CERT" --key "$ETCD_KEY" --cacert "$ETCD_CACERT" --endpoints="$ETCD_EP" snapshot save "$ETCD_BACKDIR/etcd_$CURRENT_DATE"

        # mv cmdb dumpfiles to backup directory
        echo "mv /nas/$cocktail_cmdb_pvc/somac_cmdb_dump.sql $COCKTAIL_BACKDIR/somac_cmdb_dump.$CURRENT_DATE.sql"
        mv /nas/"$cocktail_cmdb_pvc"/somac_cmdb_dump.sql "$COCKTAIL_BACKDIR"/somac_cmdb_dump."$CURRENT_DATE".sql

        echo "find $ETCD_BACKDIR -name 'etcd*' -mtime +$2 | xargs rm -rf"
        find $ETCD_BACKDIR -name "etcd*" -mtime +$2 | xargs rm -rf

        echo "find $COCKTAIL_BACKDIR -name '*cmdb-pvc*' -mtime +$2 | xargs rm -rf"
        find $COCKTAIL_BACKDIR -name "*cmdb-pvc*" -mtime +$2 | xargs rm -rf
    }

    main "${@:-}"

**2.k8s Cluster Reinstallation**

The following describes how to delete an existing k8s cluster and reinstall. In this case, only etcd, docker, kubelet, and k8s control panel (apiserver, controll-manager, and scheduler) are installed.

```
# vi cubescripts/roles/reset/tasks/main.yml
- name: Delete some files and directories
  file:
    path: "{{ item }}"
    state: absent
  with_items:
    - /etc/kubernetes/addon
    - /etc/kubernetes/manifests
    - /etc/kubernetes/*
    - /var/lib/kubelet
    - /var/lib/etcd
    - /var/lib/docker
    - /opt/cni
#    - /opt/kubernetes          // Certificate files are excluded from deletion to avoid having to regenerate certificates when reinstalling.
    - /run/kubernetes
    - /var/log/pods/
    - /etc/systemd/system/kubelet.service
    - "{{ data_root_dir }}/etcd"
    - "{{ data_root_dir }}/kubelet"
    - "{{ data_root_dir }}/docker"
    - "{{ data_root_dir }}/log"
  tags: ['files']

# cube destroy

# vi cubescripts/roles/distributecert/worker/tasks/main.yml
---
- name: Create kubernetes cert directory
  file: path={{ cert_dir }} state=directory

- name: Slurp CA certificate
  slurp: src={{ master_cert_dir }}/{{ item }}
  with_items:
    - ca.crt
    - ca.key
    - etcd.crt
    - etcd.key
#    - etcd-ca.crt      // Comment out to ensure that only the k8s ca certificate and etcd ca certificate are copied.
#    - etcd-ca.key
#    - etcd-peer.crt
#    - etcd-peer.key
  register: pki_certs
  run_once: true
  delegate_to: "{{ groups['sslhost'][0] }}"

- name: Write CA certificate to disk
  copy: dest={{ cert_dir }}/{{ item.item }} content="{{ item.content | b64decode }}"
  register: openssl_cert
  with_items: "{{ pki_certs.results }}"
  no_log: true

# vi cubescripts/cluster.yaml 에서 etcd, docker, kubelet, k8s control panel만 설치되도록 아래와 같이 수정함.
---
# This playbook deploys a kubernetes cluster with the default addons.

- hosts: yum-proxy
  roles:
    - { role: yum-proxy, tags: yum-proxy }

- hosts: masters:node
  roles:
    - { role: bootstrap-os, tags: bootstrap-os }

- hosts: masters:node
  roles:
    - { role: yum-repo, tags: yum-repo }

# install ssl cert
- hosts: sslhost
  gather_facts: false
  roles:
     - { role: sslcert, tags: sslcert }

# Install etcd
- hosts: etcd
  roles:
   - { role: etcd, tags: etcd }

# Install docker
- hosts: masters:node
  roles:
   - { role: docker, tags: docker }

# install kubernetes master services
- hosts: masters
  roles:
    - { role: master, tags: master }

# cube deploy -v debug
```

**3.etcd restore.**

```
# etcdctl --cert /etc/kubernetes/pki/etcd-peer.crt --key /etc/kubernetes/pki/etcd-peer.key \
 --cacert /etc/kubernetes/pki/etcd-ca.crt --endpoints=https://xxx.xxx.xxx.xxx:2379 --name=master \
 --initial-advertise-peer-urls="https://xxx.xxx.xxx.xxx:2380" --initial-cluster="master=https://xxx.xxx.xxx.xxx:2380" \
 --initial-cluster-token="etcd-k8-cluster" --data-dir=“/data/etcd” snapshot restore /root/backup/etcd_20180322
```

**4.Verify k8s resource activation**

```
# kubectl get pods --all-namespaces
```



