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

```
