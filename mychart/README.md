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

```
prometheus:  - создаем блок для прометея (так удобнее)
    deployment:  
        name: 
        replicas: 2     - определяем сколько реплик будет
    service:          - таких сервисов может быть сколько угодно
        type: NodePort  - используем тип NodePort чтобы в k8s создать сервис
        port: 9090      - слушает на порту 9090 (это default порт для прометея)
```

```
    image:  - тут и используем образ из dockerhub
        repository: bob4inski/prometheus
        pullPolicy: IfNotPresent
        tag: "latest"
```

```
    hpa: - блок для масштабирования
        enabled: false - включаем или выключаем 
        name:
        minReplicas: 1
        maxReplicas: 3
        targetCPUUtilizationPercentage: 50       
```

Остальное оставляем как есть вот конечный [файл](https://github.com/bob4inski/gpn-k8s-lessons/blob/main/mychart/values.yaml)

Далее смотрим все файлы в папке templates и сверяем ссылки на переменные с названиями в файле `values.yaml`
(Ориентироваться нужно по названиям файлов в папке и названиям в файле)

К примеру в файле [deployment.yml](templates/deployment.yaml#L30)

По дефолту блок такой
```
 containers:
        - name: {{ .Chart.Name }}
          securityContext:
            {{- toYaml .Values.securityContext | nindent 12 }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag | default .Chart.AppVersion }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - name: http
              containerPort: 9090 #меняем порт на тот, на котором должен работать контейнер (его и слушает сервис)
              protocol: TCP
```

Нас интересует вот эта переменная `image: "{{ .Values.image.repository }}:{{ .Values.image.tag | default .Chart.AppVersion }}"`

Сами переменные - это путь до переменной в файле `values.yaml`

`{{ .Values.image.repository }}`

Смотрим за что отвечает эта переменная по названию, сравниваем  и видим, что в `values.yaml` image находится вот тут
```
prometheus:
    ...
    image: 
        repository: bob4inski/prometheus
        pullPolicy: IfNotPresent
        tag: "latest"
```

Значит нужно использовать вместо `{{ .Values.image.repository }}`

Вот это `{{ .Values.prometheus.image.repository }}`

И так по всем переменым

Как только думаем что все сделано, то используем команду  ``helm lint mychart``

По логам можно будет понять что не так написано и поправить

Потом пишем уже другую команду  для проверки как встанет чарт ``helm install mychart  --dry-run --debug``

И только тогда, когда мы видим что все ок, то запускаем  установку `helm install mychart`

#  Прописываем команду чтобы посмотреть по какому порту подключатся
`kubectl get svc`

`minikube ip` чтобы посмотреть ip по корому можно идти на прометей

И дальше переходит по этому ip и используем порт сервиса
