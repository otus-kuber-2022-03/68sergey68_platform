# Выполнено ДЗ №5

 - [x] Основное ДЗ
 - [x] Задание со *

## В процессе сделано:
 - С помощью minikube развернут кластер в составе сингл-ноды
 - "Запущен" под с падающими пробами
 - На основе манифеста пода создан деплоймент манифест и применен
```
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      False   MinimumReplicasUnavailable
  Progressing    True    ReplicaSetUpdated
OldReplicaSets:  <none>
NewReplicaSet:   web-7cf7c96748 (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  14s   deployment-controller  Scaled up replica set web-7cf7c96748 to 1
```
 - Пробы поправлены, увеличено кол-во реплик
```
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   web-77bf85876d (3/3 replicas created)
```
- Подёргал RollingUpdate
- Применил манифест сервиса
```
smarin@otus:/root/otus/68sergey68_platform/kubernetes-networks$ kubectl get svc
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)           AGE
web-svc-cip      ClusterIP   10.104.97.69    <none>        80/TCP            90s
```
Курл прошёл
```
root@minikube:~# curl http://10.104.97.69/index.html
<html>
<head/>
<body>
<!-- IMAGE BEGINS HERE -->
<font size="-3">
```
- Включил IPVS посмотрел правила iptables, но вот `toolbox: command not found`
Однако, всё как в методичке:
```
root@minikube:~# ping -c1 10.104.97.69
PING 10.104.97.69 (10.104.97.69) 56(84) bytes of data.
64 bytes from 10.104.97.69: icmp_seq=1 ttl=64 time=0.030 ms

--- 10.104.97.69 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.030/0.030/0.030/0.000 ms
root@minikube:~# ip addr show kube-ipvs0
26: kube-ipvs0: <BROADCAST,NOARP> mtu 1500 qdisc noop state DOWN group default
    link/ether 2a:1a:3c:be:0d:e9 brd ff:ff:ff:ff:ff:ff
    inet 10.107.75.244/32 scope global kube-ipvs0
       valid_lft forever preferred_lft forever
    inet 10.96.0.10/32 scope global kube-ipvs0
       valid_lft forever preferred_lft forever
    inet 10.104.97.69/32 scope global kube-ipvs0
       valid_lft forever preferred_lft forever
    inet 10.96.0.1/32 scope global kube-ipvs0
       valid_lft forever preferred_lft forever
    inet 10.107.39.147/32 scope global kube-ipvs0
       valid_lft forever preferred_lft forever
```
- Были проблемы с запуском metallb(скачиванием образа) обновил версию до последней v12.0.1
```
{"caller":"level.go:63","event":"ipAllocated","ip":["172.17.255.1"],"level":"info","msg":"IP address assigned by controller","service":"default/web-svc-lb","ts":"2022-06-11T18:14:55.761920837Z"}
```
- Добавил маршрут, посмотрел на корабли
- Добавил два сервиса для проброса ДНС
- Установил свежий ingress-nginx
`kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.2.0/deploy/static/provider/cloud/deploy.yaml`
- Применил манифест nginx-lb.yaml
```
smarin@otus:/root/otus/68sergey68_platform/kubernetes-networks$ curl http://172.17.255.4 -v
*   Trying 172.17.255.4:80...
* TCP_NODELAY set
* Connected to 172.17.255.4 (172.17.255.4) port 80 (#0)
> GET / HTTP/1.1
> Host: 172.17.255.4
> User-Agent: curl/7.68.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 404 Not Found
< Date: Mon, 13 Jun 2022 16:30:43 GMT
< Content-Type: text/html
< Content-Length: 146
< Connection: keep-alive
<
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx</center>
</body>
</html>
* Connection #0 to host 172.17.255.4 left intact
```

- Создал headless service
`default                web-svc                              ClusterIP      None             <none>         80/TCP`
- Создал правила ingress, предварительно изменив манифест практически полностью(уж совсем стареньким показался, даже не стал пытаться применить)
```
smarin@otus:/root/otus/68sergey68_platform/kubernetes-networks$ kubectl describe ingress/web
Name:             web
Labels:           <none>
Namespace:        default
Address:
Default backend:  default-http-backend:80 (<error: endpoints "default-http-backend" not found>)
Rules:
  Host        Path  Backends
  ----        ----  --------
  *
              /web(/|$)(.*)   web-svc:8000 (172.17.0.3:8000,172.17.0.5:8000,172.17.0.7:8000)
Annotations:  nginx.ingress.kubernetes.io/rewrite-target: /$2
Events:
  Type    Reason  Age   From                      Message
  ----    ------  ----  ----                      -------
  Normal  Sync    8s    nginx-ingress-controller  Scheduled for sync
```
Всё работает!




## Как запустить проект:
 - Например, запустить команду X в директории Y

## Как проверить работоспособность:
 - Например, перейти по ссылке http://localhost:8080

## PR checklist:
 - [x] Выставлен label с темой домашнего задания
