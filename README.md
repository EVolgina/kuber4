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
```
