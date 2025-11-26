# Выполнено ДЗ № 4

 - Основное ДЗ - Хранение данных в Kubernetes: Volumes, Storages, Stateful-приложения

## В процессе сделано:
 - Написан yaml манифест для создания namespace с имененем homework
 - Написан yaml манифест для создания ConfigMap "cm_index.yaml", который настраивает nginx
 - Написан yaml манифест для создания ConfigMap "cm_key.yaml", который описывает произволный набор пары, ключ значение
 - Написан yaml манифест для создания Deployment для поднятие 3х реплик приложения kube-volume-app и который выполняет следующие действия:
        - поднимает init контейнер скачивающий index.html в директорию /init
        - поднимает вебсервер nginx на порту 80 и меняет root директорию с index.html на /etc/nginx/conf.d/default.conf для отображения содержимого в каталоге /homework/conf
        - настраивает readinessProbe в контейнере и проверяет наличие файла в директории /init/index.html
        - настраивает политику RollingUpdate, для проверки и выполнения обновления - какое количество реплик будет добавлено дополнительно для обновления и какое max количество реплик может быть недоступно
        - настраивает политику запуска подов на хостах у которых имеется метка homework=true
 - Написан yaml манифест для создания Service, перенаправление запросов от Ingress к Pod
 - Написан yaml манифест для создания PersistentVolumeClaim
 - Написан yaml манифест для создания StorageClass описывающий объект типа storageClass с provisioner https://k8s.io/minikube-hostpath и reclaimPolicy Retain
 - Написан yaml манифест для создания Ingress, принять запрос homework.otus (http://homework.otus/conf/file перенаправить на http://homework.otus/index.html). Отправить запрос на ранее настроенный Service

## Как запустить проект:
 - Клонировать репозиторий на свою рабочую станцию, зайти в каталог kubernetes-volumes
 - Проверить содержимое каталога, в нем должно присутствовать 8 файлов: 
        - namespace.yaml 
        - cm_key.yaml
        - cm_index.yaml
        - deployment.yaml
        - service.yaml
        - ingress.yaml
        - pvc.yaml
        - storageClass.yaml
 - На нодах кластера где должны запускаться наши Pod's необходимо прописать метку командой - kubectl label nodes название_вашей_ноды homework=true
 - Выполнить команду для создания Namespace с имененем homework - kubectl apply -f namespace.yaml
 - Выполнить команду для создания StorageClass - kubectl apply -f storageClass.yaml
 - Выполнить команду для создания PersistentVolumeClaim - kubectl apply -f pvc.yaml
 - Выполнить команду для создания ConfigMap отвечающего за создание ключей выполнить - kubectl apply -f cm_key.yaml
 - Выполнить команду для создания ConfigMap отвечающего за настройку nginx отображение содержимого каталога /homework/conf выполнить - kubectl apply -f cm_index.yaml
 - Выполнить команду для создания Deployment - kubectl apply -f deployment.yaml
 - Выполнить команду для создания Service - kubectl apply -f service.yaml
 - Выполнить команду для создания Ingress - kubectl apply -f ingress.yaml
 
## Как проверить работоспособность:
 - Выполнить команду для проверки статуса ресурсов - kubectl get all -n homework должны увидеть следующее:
        - Количество pod/kube-volume-app - должно быть 3 со статусом Running
        - Сервис который доступен работает и использует ClusterIP
        - Количество deployment.apps/kube-volume-app - 3
        - Количество replicaset.apps/kube-volume-app - 3
 - Выполнить команду для проверки PersistentVolumeClaim - kubectl get pv
 - Выполнить команду для проверки StorageClass - kubectl get sc
 - Выполнить команду для проверки статуса работы Ingress - kubectl get ingress -n homework должны увидеть следующее:
        - kube-volume-ingress  nginx  homework.otus   ip адрес назначенный балансировщиком или minikube   80    
 - На рабочей станции где планируем проверить работу нашего приложения в файле /etc/hosts  - прописываем ip адрес полученный в команде выше и имя хоста homework.otus (пример: 192.168.49.2 homework.otus)
 - В браузере открыть ссылку http://homework.otus/ и для проверки перенаправления попробовать ввести имя http://homework.otus/conf/file. 

## PR checklist:
 - kubernetes-volumes
