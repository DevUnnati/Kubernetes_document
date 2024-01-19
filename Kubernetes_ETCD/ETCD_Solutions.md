**Backup_Command:**

_ETCDCTL_API=3 etcdctl --endpoints 127.0.0.1:2379 snapshot save /opt/backup-etcd.db \
   --cacert=/etc/kubernetes/pki/etcd/ca.crt \
   --cert=/etc/kubernetes/pki/etcd/server.crt \
   --key=/etc/kubernetes/pki/etcd/server.key_

**Restore_Command:**

_ETCDCTL_API=3 etcdctl --data-dir <data-dir-location> snapshot restore /opt/backup-etcd.db_

**Verify_Storage_Command:**

_ETCDCTL_API=3 etcdctl --endpoints $ENDPOINT snapshot save /opt/backup-etcd.db_
   

   
<img src= "https://github.com/DevUnnati/Images/blob/main/etcd_solutions.PNG"></img>
