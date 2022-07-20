# Выполнено ДЗ №

 - [x] Основное ДЗ
 - [ ] Задание со *

## В процессе сделано:
 - С помощью Kind развернут кластер в составе сингл-ноды
```
root@otus:~/otus# kubectl get node -owide
NAME                 STATUS   ROLES                  AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE       KERNEL-VERSION      CONTAINER-RUNTIME
kind-control-plane   Ready    control-plane,master   2m27s   v1.23.4   172.18.0.2    <none>        Ubuntu 21.10   5.4.0-105-generic   containerd://1.5.10
```
- Установил csi-driver-host-path
```
root@otus:~/otus/68sergey68_platform/kubernetes-storage# git clone https://github.com/kubernetes-csi/csi-driver-host-path
root@otus:~/otus/68sergey68_platform/kubernetes-storage# cd ./csi-driver-host-path/
root@otus:~/otus/68sergey68_platform/kubernetes-storage/csi-driver-host-path# ./deploy/kubernetes-latest/deploy.sh
```
- Применил манифесты sc,pvc,pod
```
root@otus:~/otus/68sergey68_platform/kubernetes-storage/hw# kubectl get po
NAME                   READY   STATUS    RESTARTS   AGE
csi-hostpathplugin-0   8/8     Running   0          11m
storage-pod            1/1     Running   0          5s
root@otus:~/otus/68sergey68_platform/kubernetes-storage/hw# kubectl describe po storage-pod
Name:         storage-pod
Namespace:    default
Priority:     0
Node:         kind-control-plane/172.18.0.2
Start Time:   Sat, 16 Jul 2022 09:56:01 +0000
Labels:       <none>
Annotations:  <none>
Status:       Running
IP:           10.244.0.7
IPs:
  IP:  10.244.0.7
Containers:
  local-google:
    Container ID:   containerd://facfa344cf2f17ac8a8d63d9401308959dbe7cf3adbfd14ed424f5d66c0579ff
    Image:          alpine
    Image ID:       docker.io/library/alpine@sha256:686d8c9dfa6f3ccfc8230bc3178d23f84eeaf7e457f36f271ab1acc53015037c
    Port:           <none>
    Host Port:      <none>
    State:          Waiting
      Reason:       CrashLoopBackOff
    Last State:     Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Sat, 16 Jul 2022 09:56:08 +0000
      Finished:     Sat, 16 Jul 2022 09:56:08 +0000
    Ready:          False
    Restart Count:  1
    Environment:    <none>
    Mounts:
      /data from csi-hw-volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-c4cqk (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             False
  ContainersReady   False
  PodScheduled      True
Volumes:
  csi-hw-volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  storage-pvc
    ReadOnly:   false
  kube-api-access-c4cqk:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age               From               Message
  ----     ------     ----              ----               -------
  Normal   Scheduled  14s               default-scheduler  Successfully assigned default/storage-pod to kind-control-plane
  Normal   Pulled     9s                kubelet            Successfully pulled image "alpine" in 1.389454834s
  Normal   Pulling    8s (x2 over 11s)  kubelet            Pulling image "alpine"
  Normal   Created    7s (x2 over 9s)   kubelet            Created container local-google
  Normal   Started    7s (x2 over 9s)   kubelet            Started container local-google
root@otus:~/otus/68sergey68_platform/kubernetes-storage/hw# kubectl get pv
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                 STORAGECLASS   REASON   AGE
pvc-f623c473-f028-4e1b-ba18-4a170049e1db   100Mi      RWO            Delete           Bound    default/storage-pvc   csi-hw-sc               7m29s
root@otus:~/otus/68sergey68_platform/kubernetes-storage/hw# kubectl get pvc
NAME          STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
storage-pvc   Bound    pvc-f623c473-f028-4e1b-ba18-4a170049e1db   100Mi      RWO            csi-hw-sc      29m
root@otus:~/otus/68sergey68_platform/kubernetes-storage/hw# kubectl get sc
NAME                 PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
csi-hw-sc            hostpath.csi.k8s.io     Delete          WaitForFirstConsumer   false                  30m
standard (default)   rancher.io/local-path   Delete          WaitForFirstConsumer   false                  37m
```