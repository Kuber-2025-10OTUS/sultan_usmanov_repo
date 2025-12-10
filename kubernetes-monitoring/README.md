# Выполнено ДЗ № 8

 - Основное ДЗ - Мониторинг компонентов кластера и приложений, работающих в нем

## В процессе сделано:
 - Написан yaml манифест для создания ConfigMap, который настраивает nginx для предоставления информации о состоянии сервера
 - Написан yaml манифест для создания Deployment для поднятие 1ой реплик приложения kube-mon-app и который выполняет следующие действия:
        - поднимает вебсервер nginx на порту 8080
        - настраивает readinessProbe в контейнере и выполняет httpGet вызывающий /index.html
        - поднимает контейнер nginx-exporter, который обращается на под nginx на порт 8080 и собирает информацию о состоянии сервера
 - Написан yaml манифест для создания ServiceMonitor, который собирает метрики с подов
 - Написан yaml манифест для создания Service, который используется ServiceMonitor и Ingress
 - Написан yaml манифест для создания Ingress, принять запрос homework.otus (http://homework.otus/metrics_stub). Отправить запрос на ранее настроенный Service

## Как запустить проект:
 - Установить Prometheus-Operator 
       - helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
       - helm repo update
       - helm install kube-prometheus-stack prometheus-community/kube-prometheus-stack -n monitoring --create-namespace
 - Клонировать репозиторий на свою рабочую станцию, зайти в каталог kubernetes-monitoring
 - Выполнить команду для создания ConfigMap - kubectl apply -f cm.yaml
 - Выполнить команду для создания Deployment - kubectl apply -f deployment.yaml
 - Выполнить команду для создания Service - kubectl apply -f svc.yaml
 - Выполнить команду для создания ServiceMonitor - kubectl apply -f servicemonitor.yaml
 - Выполнить команду для создания Ingress - kubectl apply -f ingress.yaml
 
## Как проверить работоспособность:
 - Выполнить команду - kubectl get all -n monitoring, проверить статус установленного стека prometheus:
       - pod/alertmanager-kube-prometheus-stack-alertmanager-0          2/2     Running   0          
       - pod/kube-prometheus-stack-grafana-75c97b598c-tsttb             3/3     Running   0          
       - pod/kube-prometheus-stack-kube-state-metrics-669dcf4b9-qpthn   1/1     Running   0          
       - pod/kube-prometheus-stack-operator-58f45965c9-6rmlt            1/1     Running   0          
       - pod/kube-prometheus-stack-prometheus-node-exporter-992fz       1/1     Running   0         
       - pod/prometheus-kube-prometheus-stack-prometheus-0              2/2     Running   0          
 - Выполнить команду для проверки статуса ресурсов - kubectl get all должны увидеть следующее:
        - Количество pod/kube-mon-app  должно быть 1 со статусом Running
        - Сервис service/kube-mon-svc, который слушает на 9113/TCP,8080/TCP портах
 - Выполнить команду для проверки статуса работы Ingress - kubectl get ingress -n homework должны увидеть следующее:
        - kube-net-ingress   nginx   homework.otus   ip адрес назначенный балансировщиком или minikube   80    
 - На рабочей станции где планируем проверить работу нашего приложения в файле /etc/hosts  - прописываем ip адрес полученный в команде выше и имя хоста homework.otus (пример: 192.168.49.2 homework.otus)
 - В браузере открыть ссылку http://homework.otus/metrics_stub.
 - Также если у вас корректно установился Prometheus-Operator можно выполнить команду - kubectl port-forward svc/kube-prometheus-stack-prometheus 9090 -n monitoring, перенаправить порт на локальной машине где установлен minikube и пройти по ссылке http://127.0.0.1:9090/ в Query окне ввести запрос nginx_connections_active и нажать Execute в случае корректной работы должны увидеть результат:
       - nginx_connections_active{container="nginx-exporter", endpoint="metrics", instance="10.244.1.67:9113", job="kube-mon-svc", namespace="default", pod="kube-mon-app-5496bb7f54-xgrmn", service="kube-mon-svc"}

## PR checklist:
 - kubernetes-monitoring
