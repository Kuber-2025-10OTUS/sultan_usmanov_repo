# Выполнено ДЗ № 10

 - Основное ДЗ - GitOps - Инструменты поставки

## В процессе сделано:
 - Подготовлен argocd_install.yaml файл для установки сервиса argocd
 - Написан yaml манифест argocd_ingress.yaml для создания Ingress, принять запрос на argocd.homelab.local и перенаправить на Pod
 - Подготовлен манифест файл kubernetes_network_manifest.yaml для создания приложения kubernetes-networks в argocd
 - Подготовлен манифест файл kubernetes_templating_manifest.yaml для создания приложения kubernetes-templating в argocd

## Как запустить проект:
 - Прописать label и taint на воркер ноде, на которой планируется размещать только сервисы argocd
   * kubectl label nodes k8sworker01 node-role=infra
   * kubectl taint nodes k8sworker01 node-role=infra:NoSchedule

 - Прописать label на воркер ноде k8sworker03, согласно ДЗ, чтобы на ней развернулся сервис kubernetes-networks
   * kubectl label nodes k8sworker03 homework=true

 - Установка сервиса argocd:
   * kubectl apply -f argocd_install.yaml -n argocd

 - Выполнить команду для создания Ingress - kubectl apply -f argocd_ingress.yaml
 - Добавить запись в /etc/hosts (ваш ip и fqdn имя сервера argocd.homelab.local) - для работы с самим сервисом argocd
 - Добавить запись в /etc/hosts (ваш ip и fqdn имя сервера homework.otus) - для проверки работы установленного проекта kubernetes_network и kubernetes_templating

## Как проверить работоспособность:
 - Выполнить команду ниже и убедиться, что компоненты argocd размещаются на сервере, где мы добавили label и taint в моем случае это сервер k8sworker01
   * kubectl get all -n argocd -o wide
 - На рабочей станции где установлен kubectl и есть доступ к кластеру kubernetes выполнить команду для получения пароля к argocd
   * kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath='{.data.password}' | base64 -d; echo
 - В браузере открыть ссылку http://argocd.homelab.local
 - В разделе Settings --> Projects: 
      * добавить проект otus,
      * добавить SOURCE REPOSITORIES (репозиторий с нашим проектом, в моем случае https://github.com/Kuber-2025-10OTUS/sultan_usmanov_repo.git)
      * добавить DESTINATIONS - https://kubernetes.default.svc
      * в разделе cluster resource allow list - разрешить создание namespace - namespace resource allow list - выставить значние *
 - В разделе Settings --> Repositories добавить git репозиторий в моем случае https://github.com/Kuber-2025-10OTUS/sultan_usmanov_repo.git, добавить PAT ключ сгенерированный в GitHub
 - В разделе Applications --> нажать на кнопку NEW APP --> и с правой стороны нажать на кнопку EDIT AS YAML, удалить содержимое и добавить содержимое взятое из файла kubernetes_network_manifest.yaml и нажать сохранить, тоже самое действие повторить для манифеста kubernetes_templating_manifest.yaml.
 - В главном окне Applications мы должны увидеть два приложения kubernetes-networks и kubernetes-templating
      * kubernetes-networks - необходимо запустить синхронизацию вручную
      * kubernetes-templating - синхронизация запустится автоматически
 - После успешного выполнения, можно попробовать в браузере открыть страничку http://homework.otus

## PR checklist:
 - kubernetes-gitops