University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)  
Year: 2023/2024  
Group: K4112c  
Author: Nikitina Snezhana Vladimirovna  
Lab: Lab1  
Date of create: 1.12.2023  
Date of finished: ?.12.2023  

## Лабораторная работа №2 "Развертывание веб сервиса в Minikube, доступ к веб интерфейсу сервиса. Мониторинг сервиса"
### Описание
В данной лабораторной работе вы познакомитесь с развертыванием полноценного веб сервиса с несколькими репликами.
### Цель работы
Ознакомиться с типами "контроллеров" развертывания контейнеров, ознакомится с сетевыми сервисами и развернуть свое веб приложение.
### Ход работы:
#### 1. Создание deployment с 2 репликами контейнера
- Создан манифест deployment с 2 репликами контейнера ifilyaninitmo/itdt-contained-frontend:master. В эти реплики переданы переменные: REACT_APP_USERNAME, REACT_APP_COMPANY_NAME.
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployment
  labels:
    app: lab2
spec:
  replicas: 2
  selector: 
    matchLabels:
      app: lab2
  template:
    metadata:
      labels:
        app: lab2
    spec:
      containers:
      - name: lab2
        image: ifilyaninitmo/itdt-contained-frontend:master
        env:
        - name: REACT_APP_USERNAME
          value: Snezhana
        - name: REACT_APP_COMPANY_NAME
          value: ITMO
        ports:
        - containerPort:  3000
```
- Создан deployment из созданного ранее файла конфигурации.

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/4574139a-8022-4900-8db3-bd094d8c6393)

#### 2. Создание сервиса
- Создан манифест сервиса через который будет доступ на эти "поды".
```
apiVersion: v1
kind: Service
metadata:
  name: deployment
spec:
  type: NodePort
  selector:
    app: lab2
  ports:
    - port: 3000
      protocol: TCP
```
- Создан сервис из созданного ранее файла конфигурации.

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/fccf822e-b5a7-4b3a-848f-b1685d6213df)


#### 3. Получение доступа к контейнеру
- Для того, чтобы попасть в контейнер, необходимо прокинуть порт локального компьютера в контейнер.

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/415221c3-2609-42d5-8c2b-7b5c01d0bb87)

- Произведено подключение к контейнерам через веб браузер по ссылке http://localhost:3000.

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/c0abb09a-8142-4fa1-916d-cc1c97fcdefb)

#### 4. Вход в Vault
- Произведена провека на странице веб браузера переменных REACT_APP_USERNAME, REACT_APP_COMPANY_NAME и Container name. Переменные REACT_APP_USERNAME, REACT_APP_COMPANY_NAME такие же, какие были переданы им ранее и меняться не будут (так как им были переданы определенные значения). Container name же меняется в зависимости от того в какой из двух созданных подов идет запрос.
- Получены логи контейнеров. Они идентичны, так как являются репликами.
  
![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/d5c1cc95-5380-4eed-a584-c28da93560c0)


### Схема организации контейнеров и сервисов:  
![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/8053efb4-34bf-4e3d-8485-80bdfd464d25)

