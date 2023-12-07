University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)  
Year: 2023/2024  
Group: K4112c  
Author: Nikitina Snezhana Vladimirovna  
Lab: Lab1  
Date of create: 3.12.2023  
Date of finished: ?.12.2023  

## Лабораторная работа №4 "Сети связи в Minikube, CNI и CoreDNS"
### Описание
Это последняя лабораторная работа в которой вы познакомитесь с сетями связи в Minikube. Особенность Kubernetes заключается в том, что у него одновременно работают underlay и overlay сети, а управление может быть организованно различными CNI.
### Цель работы
Познакомиться с CNI Calico и функцией IPAM Plugin, изучить особенности работы CNI и CoreDNS.
### Ход работы:.
#### 1. Установка плагина и режима работы Multi-Node Clusters
- При запуске minikube установлен плагин CNI=calico и режим работы Multi-Node Clusters одновеременно. В рамках данной лабораторной работы развернуты 2 ноды. Для этого использована команда:
```
minikube start --network-plugin=cni --cni=calico --nodes 2 -p multinode-demo
```
![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/7651188d-fa0e-4ad3-8366-b576ea94465e)

#### 2. Проверка работы Calico
- Проведена проверка CNI плагина Calico и количества нод. На картинке показано, что CNI Calico активирован.

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/d7212f41-6f99-4899-8c4e-f713fc78c489)

- Проведена проверка работы Calico с помощью функции под названием IPAM Plugin. В начале для запущеных ранее нод указаны label по признаку стойки.

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/e0d48131-c4b5-46ae-aea4-f786d0de7fcc)

- Затем разработан манифест для Calico который бы на основе ранее указанных меток назначал бы IP адреса "подам" исходя из пулов IP адресов которые указаны в манифесте.
```
apiVersion: projectcalico.org/v3
kind: IPPool
metadata:
  name: rack-0
spec:
  cidr: 192.168.10.0/24
  ipipMode: Always
  natOutgoing: true
  nodeSelector: rack == "0"

---

apiVersion: projectcalico.org/v3
kind: IPPool
metadata:
  name: rack-1
spec:
  cidr: 192.168.20.0/24
  ipipMode: Always
  natOutgoing: true
  nodeSelector: rack == "1"
```
- Для того, чтобы создать IPPool, был установлен calicoctl. (Конфигурационный файл был загружен с официального репозитория)

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/b26099ad-0c3b-4f56-a7e1-e47f96ab89d3)

- Создан IPPool из созданного ранее манифеста.

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/2d6ae695-9e78-49c2-9d19-579e4dd09427)

- Получена информация о конфигурации IP-пула в Calico. На картинке видно, что данные соотвествуют написанному ранее манифесту.

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/3ebab3d3-a2df-4479-b8c6-a6d029a76aee)


#### 3. Создание deployment и service
- Создан deployment с 2 репликами контейнера ifilyaninitmo/itdt-contained-frontend:master и переданы переменные в эти реплики: REACT_APP_USERNAME, REACT_APP_COMPANY_NAME.
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployment
  labels:
    app: lab4
spec:
  replicas: 2
  selector: 
    matchLabels:
      app: lab4
  template:
    metadata:
      labels:
        app: lab4
    spec:
      containers:
      - name: lab4
        image: ifilyaninitmo/itdt-contained-frontend:master
        env:
        - name: REACT_APP_USERNAME
          value: Snezhana
        - name: REACT_APP_COMPANY_NAME
          value: ITMO
        ports:
        - containerPort:  3000
```

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/9b2880af-31c4-45fd-8a41-c4446cde7a36)

- Создан сервис через который будет доступ на эти "поды".
```
apiVersion: v1
kind: Service
metadata:
  name: service-lab4
spec:
  type: NodePort
  selector:
    app: lab4
  ports:
    - port: 3000
      protocol: TCP
      name: http
```
![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/60ba22eb-6c0a-4612-947d-0ec9b1ab2cc3)

#### 4. Вход в веб браузер
- Для того, чтобы попасть в контейнер, необходимо прокинуть порт локального компьютера в контейнер:

  ![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/79aa6601-8af4-4969-b382-021582f50fc9)

- После выполнения данной команды произведено подключение к контейнерам через веб браузер по ссылке http://localhost:3000. Переменные Container name и Container IP изменяются в зависимости от того в какой из двух созданных подов идет запрос.

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/383c1559-562a-4c5c-a648-2ed7ddf7fa80)

#### 5. Пинг "подов"
- Были найдены FQDN имена подов:

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/989aa405-94dd-4f56-b0b9-a56b00c2c153)

- Используя `kubectl exec` был выполнен вход в "под" и попингованы "поды" используя FQDN имя соседнего "пода". Так как поды успешно пингуются, то это значит, что сетевая связь между ними настроена корректно.

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/786298cd-551d-471d-8b0b-e95dc5f8788b)

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/f3aa39c1-cb25-42f5-8c54-f37b6be8c7b2)

### Схема организации контейнеров и сервисов:
![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/4d74403b-01ff-4b68-a7ed-f8fd9aea1884)

