# Выполнено ДЗ № 1

 - Основное ДЗ - Знакомство с Kubernetes, основные понятия и архитектура

## В процессе сделано:
 - Написан yaml манифест для создания namespace с имененем homework
 - Написан yaml манифест для создания ConfigMap, который настраивает nginx для прослушивания на порту 8000 и отображает содержимое каталога /homework
 - Написан yaml манифест для создания Pod kube-intro-app, который поднимает вебсервер на порту 8000 и init контейнер скачивающий index.html

## Как запустить проект:
 - Клонировать репозиторий на свою рабочую станцию, зайти в каталог kubernetes-intro
 - Проверить содержимое каталога, в нем должно присутствовать 3 файла: namespace.yaml, cm.yaml, pod.yaml
 - Выполнить команду для создания namespace с имененем homework - kubectl apply -f namespace.yaml
 - Выполнить команду для создания ConfigMap - kubectl apply -f cm.yaml
 - Выполнить команду для создания Pod - kubectl apply -f pod.yaml

## Как проверить работоспособность:
 - Проверить статус POD'а командой - kubectl get pod -n homework (корретный статус пода - kube-intro-app   1/1    Running)
 - Получить IP пода командой - kubectl describe pod kube-intro-app -n homework | grep -w IP: | head -1
 - Проверить локально где у вас развернут k0s, minikube можно командой - curl http://IP полученный выше:8000
 - Для настройки доступности вашего веб сервера с другой рабочей станции, можно выполнить команду - kubectl port-forward --address 0.0.0.0 pod/kube-intro-app 30156:8000 -n homework
 - В браузере открыть ссылку http://ip адрес рабочей станции где развернут k0s, minikube :30156/

## PR checklist:
 - [kubernetes-intro] Выставлен label с темой домашнего задания
