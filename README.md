# Домашнее задание к занятию «Сетевое взаимодействие в K8S. Часть 1»
## Задание 1. Создать Deployment и обеспечить доступ к контейнерам приложения по разным портам из другого Pod внутри кластера
- Создать Deployment приложения, состоящего из двух контейнеров (nginx и multitool), с количеством реплик 3 шт.
- Создать Service, который обеспечит доступ внутри кластера до контейнеров приложения из п.1 по порту 9001 — nginx 80, по 9002 — multitool 8080.
- Создать отдельный Pod с приложением multitool и убедиться с помощью curl, что из пода есть доступ до приложения из п.1 по разным портам в разные контейнеры.
- Продемонстрировать доступ с помощью curl по доменному имени сервиса.
- Предоставить манифесты Deployment и Service в решении, а также скриншоты или вывод команды п.4.
### Ответ:
- создаем Deployment с двумя контейнерами (nginx и multitool) и требуемым количеством реплик (3 шт.) [dep.yaml]()
- создаем манифеста Service, файл srv.yaml [srv.yaml]()
```
devops@WORKBOOK:/mnt/kube/kub4$ sudo nano dep.yaml
devops@WORKBOOK:/mnt/kube/kub4$ sudo nano svr.yaml
devops@WORKBOOK:/mnt/kube/kub4$ kubectl apply -f dep.yaml
deployment.apps/myapp-deployment created
devops@WORKBOOK:/mnt/kube/kub4$ kubectl apply -f svr.yaml
service/myapp-service created
devops@WORKBOOK:/mnt/kube/kub4$ kubectl get svc myapp-service
NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)             AGE
myapp-service   ClusterIP   10.152.183.26   <none>        9001/TCP,9002/TCP   30m



```
