# Выполнено ДЗ № 5

 - Основное ДЗ - Основы безопасности в Kubernetes 

## В процессе сделано:
 - Написан yaml манифест для создания namespace с имененем homework
 - Написан yaml манифест для создания ConfigMap, который настраивает nginx
 - Написан yaml манифест для создания Serviceaccount с именем cd (роль admin в рамках namespace homework)
 - Написан yaml манифест для создания Serviceaccount с именем monitoring (кластер роль дающая доступ к endpoint /metrics вашего кластера), от имени этой учетной записи будут запускаться поды и отображать метрики кластера
 - Написан yaml манифест для создания Deployment для поднятие 3х реплик приложения kube-net-app и который выполняет следующие действия:
        - поднимает init контейнер скачивающий metrics.html в директорию /init, метрики берутся из http://kube-dns.kube-system.svc.cluster.local:9153/metrics
        - поднимает вебсервер nginx на порту 8000 и отображает содержимое metrics.html
        - настраивает readinessProbe в контейнере и проверяет наличие файла /homework/metrics.html
        - настраивает политику RollingUpdate, для проверки и выполнения обновления - какое количество реплик будет добавлено дополнительно для обновления и какое max количество реплик может быть недоступно
        - настраивает политику запуска подов на хостах у которых имется метка homework=true
 - Написан yaml манифест для создания Service, перенаправление запросов от Ingress к Pod
 - Написан yaml манифест для создания Ingress, принять запрос homework.otus (http://homework.otus/metrics.html перенаправить на http://homework.otus/metrics.html и отобразить содержимое). Отправить запрос на ранее настроенный Service

## Как запустить проект:
 - Клонировать репозиторий на свою рабочую станцию, зайти в каталог kubernetes-security
 - Проверить содержимое каталога, в нем должно присутствовать 9 файлов: 
        - namespace.yaml 
        - cm.yaml
        - deployment.yaml
        - service.yaml
        - ingress.yaml
        - serviceaccount_cd.yaml
        - serviceaccount_monitoring.yaml
        - kubeconfig.yaml
        - token
 - Выполнить команду minikube addons list - отобразить список установленных addon, если metrics-server addon не включен, необходимо выполнить команду "minikube addons enable metrics-server"
 - На нодах кластера где должны запускаться наши Pod's необходимо прописать метку командой - kubectl label nodes название_вашей_ноды homework=true
 - Выполнить команду для создания Namespace с имененем homework - kubectl apply -f namespace.yaml
 - Сгенерировать для service account "cd" токен с временем действия 1 день и сохранить его в файл "token", команда для получения токена - kubectl create token cd --namespace homework --duration=86400s
 - Выполнить команду для создания Serviceaccount с именем monitoring и прикрепленной ролью -  kubectl apply -f serviceaccount_monitoring.yaml
 - Выполнить команду для создания Serviceaccount с именем cd и прикрепленной ролью - kubectl apply -f serviceaccount_cd.yaml
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
 - В браузере открыть ссылку http://homework.otus/metrics.html должны отобразиться метрики кластера.
 - Выполнить команду export KUBECONFIG=${PWD}/kubeconfig.yaml для работы с конфиг файлом созданного для пользователя "cd", у которого должны быть права admin в рамках namespace homework
       * Для проверки выполняем команду:
       -- kubectl get pods -n homework
          Должны отобразиться поды в namespace homework
       -- kubectl get pods -n default
          Должны получить вот такую ошибку, означающую что у пользователя "cd" нет прав для работы с ресурсами в другом namespace - Error from server (Forbidden): pods is forbidden: User "system:serviceaccount:homework:cd" cannot list resource "pods" in API group "" in the namespace "default"

## PR checklist:
 - kubernetes-security
