# Домашнее задание к занятию «Сетевое взаимодействие в K8S. Часть 1»
## Задание 1. Создать Deployment и обеспечить доступ к контейнерам приложения по разным портам из другого Pod внутри кластера
- Создать Deployment приложения, состоящего из двух контейнеров (nginx и multitool), с количеством реплик 3 шт.
- Создать Service, который обеспечит доступ внутри кластера до контейнеров приложения из п.1 по порту 9001 — nginx 80, по 9002 — multitool 8080.
- Создать отдельный Pod с приложением multitool и убедиться с помощью curl, что из пода есть доступ до приложения из п.1 по разным портам в разные контейнеры.
- Продемонстрировать доступ с помощью curl по доменному имени сервиса.
- Предоставить манифесты Deployment и Service в решении, а также скриншоты или вывод команды п.4.
### Ответ:
- создаем Deployment с двумя контейнерами (nginx и multitool) и требуемым количеством реплик (3 шт.) [apps.yaml]()
- создаем манифеста Service, файл srv.yaml [service.yaml]()
```
vagrant@vagrant:~/kube/zad3$ kubectl apply -f apps.yaml
deployment.apps/my-deployment created
vagrant@vagrant:~/kube/zad3$ kubectl apply -f service.yaml
service/my-service created
vagrant@vagrant:~/kube/zad3$ kubectl apply -f mult.yaml
pod/multitool-pod created
vagrant@vagrant:~/kube/zad3$ kubectl get pods
NAME                           READY   STATUS    RESTARTS   AGE
multitool-7f8c7df657-h2m9s     1/1     Running   0          69m
my-deployment-c57565bf-5mtt7   2/2     Running   0          62s
my-deployment-c57565bf-ghfs9   2/2     Running   0          62s
my-deployment-c57565bf-cvstv   2/2     Running   0          63s
multitool-pod                  1/1     Running   0          11s
vagrant@vagrant:~/kube/zad3$ kubectl get service
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)             AGE
kubernetes   ClusterIP   10.152.183.1    <none>        443/TCP             20d
service      ClusterIP   10.152.183.43   <none>        8080/TCP            29m
my-service   ClusterIP   10.152.183.48   <none>        9001/TCP,9002/TCP   73s
vagrant@vagrant:~/kube/zad3$ kubectl get deployment
NAME            READY   UP-TO-DATE   AVAILABLE   AGE
multitool       1/1     1            1           6d2h
my-deployment   3/3     3            3           105s
vagrant@vagrant:~/kube/zad3$ kubectl describe pod multitool-pod
Name:             multitool-pod
Namespace:        default
Priority:         0
Service Account:  default
Node:             vagrant/10.0.2.15
Start Time:       Sat, 24 Feb 2024 11:48:21 +0000
Labels:           <none>
Annotations:      cni.projectcalico.org/containerID: e7410bcf50d287c615582e8dcb0a405b275c6fd16fc8266cf3bec347c8c1c049
                  cni.projectcalico.org/podIP: 10.1.52.147/32
                  cni.projectcalico.org/podIPs: 10.1.52.147/32
Status:           Running
IP:               10.1.52.147
IPs:
  IP:  10.1.52.147
Containers:
  multitool-container:
    Container ID:  containerd://e0b6e60d05111d0b6a43c1a7a940b7a56d9e9faaf699e4b096777d4142fd6f7b
    Image:         wbitt/network-multitool:latest
    Image ID:      docker.io/wbitt/network-multitool@sha256:d1137e87af76ee15cd0b3d4c7e2fcd111ffbd510ccd0af076fc98dddfc50a735
    Port:          8080/TCP
    Host Port:     0/TCP
    Command:
      sh
      -c
      echo The multitool is running! && sleep 3600
    State:          Running
      Started:      Sat, 24 Feb 2024 11:48:33 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-hdnlw (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  kube-api-access-hdnlw:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:                      <none>
vagrant@vagrant:~/kube/zad3$ kubectl describe svc my-service
Name:              my-service
Namespace:         default
Labels:            <none>
Annotations:       <none>
Selector:          app=my-app
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.152.183.48
IPs:               10.152.183.48
Port:              nginx  9001/TCP
TargetPort:        80/TCP
Endpoints:         10.1.52.129:80,10.1.52.160:80,10.1.52.191:80
Port:              multitool  9002/TCP
TargetPort:        8080/TCP
Endpoints:         10.1.52.129:8080,10.1.52.160:8080,10.1.52.191:8080
Session Affinity:  None
Events:            <none>
vagrant@vagrant:~/kube/zad3$ kubectl exec -it multitool-pod -- /bin/bash
multitool-pod:/# curl my-service:9001
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
multitool-pod:/# curl my-service:9002
curl: (7) Failed to connect to my-service port 9002 after 15 ms: Couldn't connect to server
multitool-pod:/# ping my-service
PING my-service.default.svc.cluster.local (10.152.183.48) 56(84) bytes of data.
64 bytes from my-service.default.svc.cluster.local (10.152.183.48): icmp_seq=1 ttl=62 time=30.9 ms
64 bytes from my-service.default.svc.cluster.local (10.152.183.48): icmp_seq=2 ttl=62 time=5.49 ms
64 bytes from my-service.default.svc.cluster.local (10.152.183.48): icmp_seq=3 ttl=62 time=2.47 ms
64 bytes from my-service.default.svc.cluster.local (10.152.183.48): icmp_seq=4 ttl=62 time=2.87 ms
64 bytes from my-service.default.svc.cluster.local (10.152.183.48): icmp_seq=5 ttl=62 time=2.45 ms
64 bytes from my-service.default.svc.cluster.local (10.152.183.48): icmp_seq=6 ttl=62 time=1.57 ms
64 bytes from my-service.default.svc.cluster.local (10.152.183.48): icmp_seq=7 ttl=62 time=2.86 ms
64 bytes from my-service.default.svc.cluster.local (10.152.183.48): icmp_seq=8 ttl=62 time=13.3 ms
64 bytes from my-service.default.svc.cluster.local (10.152.183.48): icmp_seq=9 ttl=62 time=2.80 ms
64 bytes from my-service.default.svc.cluster.local (10.152.183.48): icmp_seq=10 ttl=62 time=7.16 ms
64 bytes from my-service.default.svc.cluster.local (10.152.183.48): icmp_seq=11 ttl=62 time=2.51 ms
64 bytes from my-service.default.svc.cluster.local (10.152.183.48): icmp_seq=12 ttl=62 time=5.45 ms
64 bytes from my-service.default.svc.cluster.local (10.152.183.48): icmp_seq=13 ttl=62 time=3.18 ms
64 bytes from my-service.default.svc.cluster.local (10.152.183.48): icmp_seq=14 ttl=62 time=2.30 ms
```
