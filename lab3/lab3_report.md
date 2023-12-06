University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)  
Year: 2023/2024  
Group: K4112c  
Author: Nikitina Snezhana Vladimirovna  
Lab: Lab1  
Date of create: 1.12.2023  
Date of finished: ?.12.2023  

## Лабораторная работа №3 "Сертификаты и "секреты" в Minikube, безопасное хранение данных."
### Описание
В данной лабораторной работе вы познакомитесь с сертификатами и "секретами" в Minikube, правилами безопасного хранения данных в Minikube.
### Цель работы
Познакомиться с сертификатами и "секретами" в Minikube, правилами безопасного хранения данных в Minikube.
### Ход работы:
#### 1. Создание configMap
- Создан configMap с переменными: REACT_APP_USERNAME, REACT_APP_COMPANY_NAME.
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: cm-lab3
data:
  REACT_APP_USERNAME: Snezhana
  REACT_APP_COMPANY_NAME: ITMO
```

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/cee75cca-c703-4515-b35e-93ebbc3ab183)

#### 2. Создание replicaSet с 2 репликами контейнера
- Создан replicaSet с 2 репликами контейнера ifilyaninitmo/itdt-contained-frontend:master и используя ранее созданный configMap передать переменные REACT_APP_USERNAME, REACT_APP_COMPANY_NAME.
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: lab3
spec:
  replicas: 2
  selector:
    matchLabels:
      app: lab3
  template:
    metadata:
      labels:
        app: lab3
    spec:
      containers:
      - name: lab3
        image: ifilyaninitmo/itdt-contained-frontend:master
        envFrom:
          - configMapRef:
              name: cm-lab3
        ports:
          - containerPort: 3000
```

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/1ba28b55-88f4-40d0-b442-8e625e69c6e2)

- Создан cервис через который будет доступ на эти "поды".
```
apiVersion: v1
kind: Service
metadata:
  name: lab3
spec:
  type: NodePort
  selector:
    app: lab3
  ports:
    - port: 3000
      protocol: TCP
```

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/2356dd42-281e-48bd-b061-41c9be354aa4)

#### 3. Генерирование сертификата
- Включен minikube addons enable ingress.

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/8a083591-5c46-47e2-b69c-2be2b47782f8)

- Сгенерирован TLS сертификат.

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/3df5762f-df67-451e-8031-5f443a8b8de7)

- Созданный сертификат импортирован в minikube.

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/d418805c-3574-4957-9342-df5277bf1f15)


#### 4. Создание ingress
- Создан манифест ingress в minikube, где указан ранее импортированный сертификат, FQDN по которому будет воспроизводиться вход и имя сервиса который был создан ранее.
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: lab3
spec:
  tls:
  - hosts:
    - lab3.local
    secretName: tls-secret
  rules:
  - host: lab3.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: lab3
            port:
              number: 3000
```
- Создан ingress из созданного ранее файла конфигурации.

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/155b974b-35d3-4e95-a3e5-fc07d6325224)

#### 4. Создание ingress
- Создать ingress в minikube, где указан ранее импортированный сертификат, FQDN по которому вы будете заходить и имя сервиса который вы создали ранее.
Если вы делаете эту работу на Windows/macOS для доступа к ingress вам необходимо использовать команду minikube tunnel к созданному ingress. Если вы делаете эту работу на Windows/macOS для доступа к ingress вам необходимо в hosts добавить ip address localhost и ваш FQDN. Если установлен Linux, то нужно указывать minikube ip.

#### 5. Вход в веб приложение
- В hosts пропишите FQDN и IP адрес вашего ingress и попробуйте перейти в браузере по FQDN имени.

Войдите в веб приложение по вашему FQDN используя HTTPS и проверьте наличие сертификата.

Обычно в браузере это маленький замочек рядом с FQDN сайта, нажмите на него и сделайте скриншот с информацией.

### Схема организации контейнеров и сервисов:
ФОТО
