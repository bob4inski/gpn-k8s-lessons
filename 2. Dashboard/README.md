## dashboard
### 1. Установка dashboard
Запустите на установку yaml-файл, результатом будет установка дополнительных сервисов k8s:
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```
Вывод будет выглядеть так:
```
namespace/kubernetes-dashboard created
serviceaccount/kubernetes-dashboard created
service/kubernetes-dashboard created
secret/kubernetes-dashboard-certs created
secret/kubernetes-dashboard-csrf created
secret/kubernetes-dashboard-key-holder created
configmap/kubernetes-dashboard-settings created
role.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrole.rbac.authorization.k8s.io/kubernetes-dashboard created
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
deployment.apps/kubernetes-dashboard created
service/dashboard-metrics-scraper created
deployment.apps/dashboard-metrics-scraper created
```
### 2. Создание сервисного аккаунта
Запустите на установку yaml-файл, который создаст сервисный аккаунт и настроит роль:
```
kubectl apply -f https://raw.githubusercontent.com/Ramazeca/sirius-k8s-lessons/main/2.%20Dashboard/dashboard-adminuser.yaml
```
При успешном создании сервисного аккаунта вывод будет выглядеть так:
```
serviceaccount/admin-user created
clusterrolebinding.rbac.authorization.k8s.io/admin-user created
```
### 3. Генерация токена
Теперь нам нужно сгенерировать токен, который мы сможем использовать для входа в dashboard. Для этого выполните следующую команду:
```
kubectl -n kubernetes-dashboard create token admin-user
```
Вывод будет примерно таким:
```
eyJhbGciOiJSUzI1NiIsImtpZCI6ImxvaW5OVmhfVEc1OUpzSzRocDNMSzhfNldTZWVhVEJULUx2b2tzVC14LWcifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiXSwiZXhwIjoxNjgzNzEwNzk5LCJpYXQiOjE2ODM3MDcxOTksImlzcyI6Imh0dHBzOi8va3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FsIiwia3ViZXJuZXRlcy5pbyI6eyJuYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsInNlcnZpY2VhY2NvdW50Ijp7Im5hbWUiOiJhZG1pbi11c2VyIiwidWlkIjoiZTk1ZGFhODEtM2U4OS00NTIxLTg3MDItMjE3NzYzMzlhZTk3In19LCJuYmYiOjE2ODM3MDcxOTksInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDphZG1pbi11c2VyIn0.RaYQUBtpmxTaCPVSnuJH4n7ysc2PVDyOGT-_yYZ4f_kHRHhjsHd37SjYqXLDSHo577_Zm5Bgy03iqvsMxkzK426IOUaPRqg64iQz1WNA2JhwJ6QYUX_rPRDjv-saKMLYZfjXND6xjjjpxeJI9OEC3xx1SmwdTIswGC45YGv_lmZta3qUzYLuRRPm5s6CZkL80BvghHwSGQHuNxXbtblxexTTPNOf2_K6o7ejdt6NrE2b29LcKmHVTLALki5RQZtINTnZ7MmYklvr62Y4jtuHOjkdr5NZNhzJvYg-KWTQ3zKBnNp0IUdA6uXtRwOvMiz46M2H8Bq_NhhyKoWOty1Rig
```
### 4. Включение прокси
Для этого выполните команду:
```
kubectl proxy
```
Вывод должен получиться таким:
```
Starting to serve on 127.0.0.1:8001
```
### 5. Настройка аутентификации по созданному токену
Перейдите по ссылке ниже:
```
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login
```
Введите ранее созданный токен в поле "Enter token" и нажмите "Sign In".

Когда вы откроете Dashboard на пустом кластере, вы увидите страницу приветствия, кнопку для развертывания вашего первого приложения. Кроме того, вы можете посмотреть, какие системные приложения запущены по умолчанию в пространстве имен kube-system вашего кластера, например, сам Dashboard.
