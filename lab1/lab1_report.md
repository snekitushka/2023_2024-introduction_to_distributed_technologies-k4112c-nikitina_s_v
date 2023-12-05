University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)  
Year: 2023/2024  
Group: K4112c  
Author: Nikitina Snezhana Vladimirovna  
Lab: Lab1  
Date of create: 30.11.2023  
Date of finished: 30.11.2023  

## Лабораторная работа №1 "Установка Docker и Minikube, мой первый манифест"
### Описание
Это первая лабораторная работа в которой вы сможете протестировать Docker, установить Minikube и развернуть свой первый "под".
### Цель работы
Ознакомиться с инструментами Minikube и Docker, развернуть свой первый "под".
### Ход работы:
#### 1. Подготовка
- Для выполнения лабораторной работы на рабочий компьютер установлены Docker и Minikube.
- После установки развернут minikube cluster. Для этого была использована команда:
```
minikube start
```

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/d837a7e3-86b7-4cfc-b24c-3667f460f57a)


#### 2. Создание манифеста и запуск "пода"
- Создан файл описания "пода" (манифест) `vault.yaml`. Для первого манифеста выбран образ HashiCorp Vault.
```
apiVersion: v1
kind: Pod
metadata:
  name: vault
  labels:
    app: vault
spec:
  containers:
  - name: vault
    image: vault:1.13.3
    ports:
    - containerPort:  8200
```
?Описание штук
- Затем создан "под" из созданного ранее файла конфигурации, выполнив команду:
```
kubectl apply -f vault.yaml
```
![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/de5759b2-97b1-4fbc-8b7f-64974b4bd7ef)

#### 3. Получение доступа к контейнеру
- Создан сервис для доступа к этому контейнеру:
```
minikube kubectl -- expose pod vault --type=NodePort --port=8200
```
- Для того, чтобы попасть в контейнер, необходимо прокинуть порт локального компьютера в контейнер:
```
minikube kubectl -- port-forward service/vault 8200:8200
```
- После выполнения данной команды произведен переход в vault по ссылке http://localhost:8200.

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/6c79c1aa-ecea-46a6-88c9-92433a522128)

#### 4. Вход в Vault
- Чтобы войти в Vault, необходимо найти токен. Для этого получены логи из контейнера в указанном поде vault:
```
kubectl logs vault
```
![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/fe157fa7-4599-4513-824d-d80382c567a7)

- Получен доступ с помощью данных из Root Token.

![image](https://github.com/snekitushka/2023_2024-introduction_to_distributed_technologies-k4112c-nikitina_s_v/assets/65435279/93020a67-32ad-45c4-ae6c-4fcbb2a90471)

### Схема организации контейнеров и сервисов:
ФОТО
### Ответы на вопросы:
<ins> 1. Что сейчас произошло и что сделали команды указанные ранее? </ins>  
Ответ  
<ins> 2. Где взять токен для входа в Vault? </ins>  
Ответ  




   
