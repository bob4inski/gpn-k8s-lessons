## Для того чтобы создать  и запустить свой Helm нужно сделать следущие шаги:

1. [установить](https://helm.sh/docs/intro/install/) helm

    в случае с Ubuntu есть 2 варианта:

```
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```
 или

``
sudo snap install helm --classic
``

2. Создать папку, в которой будете создавать свой Chart и проинициализировать Chart

`mkdir mychart && helm create mychart`
    tests
В итоге будет создана такая структура для чарта

```
.
└── mychart
    ├── charts
    ├── templates
    │   ├── deployment.yaml
    │   ├── _helpers.tpl
    │   ├── hpa.yaml
    │   ├── NOTES.txt
    │   ├── ingress.yaml
    │   ├── service-account.yaml
    |   ├── service.yaml
    │   └── tests
    │       └── interaction-test.yaml  
    ├── Chart.yaml
    └── values.yaml
```
3. Зайти на [dockerhub](https://hub.docker.com/) и посмотреть какое приложение хоти развернуть

Для примера возьму свой собранный [prometheus](https://hub.docker.com/repository/docker/bob4inski/prometheus)

4.  В файл `Chart.yml` достаточно будет записать вот эти строки

```
apiVersion: v2
name: mychart
description: A Helm chart for Kubernetes
type: application
version: 0.1.0
appVersion: "1.16.0"
```

5. А дальше начинается работа с файлом `values.yaml`

А теперь пойдем по блокам 





