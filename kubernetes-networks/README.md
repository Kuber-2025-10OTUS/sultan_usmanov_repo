# Выполнено ДЗ № 3

 - Основное ДЗ - Сетевая подсистема и сущности Kubernetes

## В процессе сделано:
 - Написан yaml манифест для создания namespace с имененем homework
 - Написан yaml манифест для создания ConfigMap, который настраивает nginx
 - Написан yaml манифест для создания Deployment для поднятие 3х реплик приложения kube-net-app и который выполняет следующие действия:
        - поднимает init контейнер скачивающий index.html в директорию /init
        - поднимает вебсервер nginx на порту 8000 и меняет root директорию с index.html на /homework
        - настраивает readinessProbe в контейнере и выполняет httpGet вызывающий /index.html
        - настраивает политику RollingUpdate, для проверки и выполнения обновления - какое количество реплик будет добавлено дополнительно для обновления и какое max количество реплик может быть недоступно
        - настраивает политику запуска подов на хостах у которых имется метка homework=true
 - Написан yaml манифест для создания Service, перенаправление запросов от Ingress к Pod
 - Написан yaml манифест для создания Ingress, принять запрос homework.otus (http://homework.otus/homepage перенаправить на http://homework.otus/index.html). Отправить запрос на ранее настроенный Service

## Как запустить проект:
 - Клонировать репозиторий на свою рабочую станцию, зайти в каталог kubernetes-networks
 - Проверить содержимое каталога, в нем должно присутствовать 5 файлов: 
        - namespace.yaml 
        - cm.yaml
        - deployment.yaml
        - service.yaml
        - ingress.yaml
 - На нодах кластера где должны запускаться наши Pod's необходимо прописать метку командой - kubectl label nodes название_вашей_ноды homework=true
 - Выполнить команду для создания Namespace с имененем homework - kubectl apply -f namespace.yaml
 - Выполнить команду для создания ConfigMap - kubectl apply -f cm.yaml
 - Выполнить команду для создания Deployment - kubectl apply -f deployment.yaml
 - Выполнить команду для создания Service - kubectl apply -f service.yaml
 - Выполнить команду для создания Ingress - kubectl apply -f ingress.yaml
 
## Как проверить работоспособность:
 - Выполнить команду для проверки статуса ресурсов - kubectl get all -n homework должны увидеть следующее:
        - Количество pod/kube-net-app- должно быть 3 со статусом Running
        - Сервис который доступен работает и использует ClusterIP
        - Количество deployment.apps/kube-net-app - 3
        - Количество replicaset.apps/kube-net-app - 3
 - Выполнить команду для проверки статуса работы Ingress - kubectl get ingress -n homework должны увидеть следующее:
        - kube-net-ingress   nginx   homework.otus   ip адрес назначенный балансировщиком или minikube   80    
 - На рабочей станции где планируем проверить работу нашего приложения в файле /etc/hosts  - прописываем ip адрес полученный в команде выше и имя хоста homework.otus (пример: 192.168.49.2 homework.otus)
 - В браузере открыть ссылку http://homework.otus/ и для проверки перенаправления попробовать ввести имя http://homework.otus/homepage. 

## PR checklist:
 - kubernetes-networks
