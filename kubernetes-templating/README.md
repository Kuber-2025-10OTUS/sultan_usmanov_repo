# Выполнено ДЗ № 6

 - Основное ДЗ - Управление приложениями в k8s. Пакетный менеджер Helm 

## В процессе сделано:
 - Шаблонизирован yaml манифест для создания ConfigMap, который настраивает nginx
 - Шаблонизирован yaml манифест для создания Deployment для поднятие 3х реплик приложения kube-net-app и который выполняет следующие действия:
        - поднимает init контейнер скачивающий index.html в директорию /init
        - поднимает вебсервер nginx на порту 8000 и меняет root директорию с index.html на /homework
        - настраивает readinessProbe в контейнере и выполняет httpGet вызывающий /index.html
        - настраивает политику RollingUpdate, для проверки и выполнения обновления - какое количество реплик будет добавлено дополнительно для обновления и какое max количество реплик может быть недоступно
        - настраивает политику запуска подов на хостах у которых имется метка homework=true
 - Написан NOTES.txt в случае успешной установки, пользователь увидит ссылку на ресурс - http://homework.otus/ 
 - Шаблонизирован yaml манифест для создания Service, перенаправление запросов от Ingress к Pod
 - Шаблонизирован yaml манифест для создания Ingress, принять запрос homework.otus (http://homework.otus/ перенаправить на http://homework.otus/index.html). Отправить запрос на ранее настроенный Service

## Как запустить проект helm:
 - Клонировать репозиторий на свою рабочую станцию, зайти в каталог kubernetes-templating
 - Так как требовалось выполнить два задания, для порядка в внутри каталога kubernetes-templating я создал:
        - kubernetes-templating           - работа со стандартным helm 
        - kubernetes-templating-helmfile  - работа с helmfile
 - На нодах кластера где должны запускаться наши Pod's необходимо прописать метку командой - kubectl label nodes название_вашей_ноды homework=true
 - В корневом каталоге kubernetes-templating выполняем команду - helm install kube-net-app ./kubernetes-templating/ -f ./kubernetes-templating/values.yaml --namespace homework --create-namespace

 ## Как запустить проект helmfile:
 - Необходимо зайти в каталог kubernetes-templating/kubernetes-templating-helmfile и выполнить команды согласно описания ниже:
        - Для запуска подов в среде prod выполняем команду - helmfile --environment prod apply
        - Для запуска подов в среде dev выполняем команду - helmfile --environment dev apply

## Как проверить работоспособность helm:
 - Выполнить команду для проверки статуса ресурсов - kubectl get all -n homework должны увидеть следующее:
        - Количество pod/kube-net-app- должно быть 3 со статусом Running
        - Сервис который доступен работает и использует ClusterIP
        - Количество deployment.apps/kube-net-app - 3
        - Количество replicaset.apps/kube-net-app - 3
 - Также должны запуститься поды - kube-net-app-jenkins т.к в задании было указано (Добавьте в свой чарт сервис-зависимость из доступных community-чартов) я нашел jenkins
 - Выполнить команду для проверки статуса работы Ingress - kubectl get ingress -n homework должны увидеть следующее:
        - kube-net-ingress   nginx   homework.otus   ip адрес назначенный балансировщиком или minikube   80    
 - На рабочей станции где планируем проверить работу нашего приложения в файле /etc/hosts  - прописываем ip адрес полученный в команде выше и имя хоста homework.otus (пример: 192.168.49.2 homework.otus)
 - В браузере открыть ссылку http://homework.otus/.

## Как проверить работоспособность helmfile:
- На рабочей станции обязательно должен быть установлен helmfile и плагин diff
- Выполнить команду для проверки статуса ресурсов для среды prod - kubectl get all -n prod
        - Количество подов в prod - должно быть 5
- Выполнить команду для проверки статуса ресурсов для среды dev - kubectl get all -n dev
        - Количество подов в dev - должно быть 1

## PR checklist:
 - kubernetes-templating
