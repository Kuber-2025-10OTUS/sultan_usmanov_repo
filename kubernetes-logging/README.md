# Выполнено ДЗ № 9

 - Основное ДЗ - Сервисы централизованного логирования для Kubernetes

## В процессе сделано:
 - Подготовлены values.yaml файлы для деплоя компонентов согласно ДЗ
 - В loki-values.yaml был включен и настроен параметр minio
 - В качестве распределенно системы хранения данных я настроил Longhorn 
 - Написан yaml манифест для создания Ingress, принять запрос на grafana.homelab.local и перенаправить на Pod
 - Написан yaml манифест для создания Ingress, принять запрос на minio.homelab.local и перенаправить на Pod

## Как запустить проект:
 - Прописать label и taint на воркер ноде, на которой планируется размещать только сервисы loki
   kubectl label nodes k8sworker-01 node-role=infra
   kubectl taint nodes k8sworker-01 node-role=infra:NoSchedule

 - Добавить репозиторий из которого палниурем установить нужные нам компоненты:
   helm repo add grafana https://grafana.github.io/helm-charts

 - Просмотреть список компонентов:
   helm search repo grafana

 - Выкачать чарты и в дальнейшем работать с ними:
   helm pull grafana/grafana --untar
   helm pull grafana/loki --untar
   helm pull grafana/promtail --untar

 - Получить values файлы:
   helm show values grafana/loki > loki-values.yaml
   helm show values grafana/grafana > grafana-values.yaml
   helm show values grafana/promtail > promtail-values.yaml

 - Установка компонентов:
   helm upgrade --install loki grafana/loki -f loki-values.yaml --set loki.useTestSchema=true
   helm upgrade --install grafana grafana/grafana -f grafana-values.yaml 
   helm upgrade --install promtail grafana/promtail -f promtail-values.yaml

 - Удаление компонентов:
   helm uninstall loki grafana/loki
   helm uninstall grafana grafana/grafana
   helm uninstall promtail grafana/promtail

 - Выполнить команду для создания Ingress - kubectl apply -f ingress-grafana.yaml
 - Выполнить команду для создания Ingress - kubectl apply -f ingress-minio.yaml
 - Добавить запись в /etc/hosts (ваш ip и fqdn имя сервера grafana.homelab.local)
 - Добавить запись в /etc/hosts (ваш ip и fqdn имя сервера minio.homelab.local)

## Как проверить работоспособность:
 - Выполнить команду - kubectl get all -n default компоненты loki и grafana должны размещаться на сервере где мы добавили label и taint в моем случае это сервер k8sworker-01
 - На рабочей станции где установлен kubectl и есть доступ к кластеру kubernetes выполнить команду для получения пароля к grafana - kubectl get secret --namespace loki loki-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
 - В браузере открыть ссылку http://grafana.homelab.local в разделе Datastore из списка выбрать Loki и прописать ссылку http://loki-gateway.loki.svc.cluster.local после наживаем Save and Test.

## PR checklist:
 - kubernetes-logging