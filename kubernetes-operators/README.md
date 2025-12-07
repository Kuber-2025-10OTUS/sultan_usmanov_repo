# Выполнено ДЗ № 7

 - Основное ДЗ - Custom Resource Definitions

## В процессе сделано:
 - Написан yaml манифест для создания Deployment, который будет использоваться оператором
 - Написан yaml манифест для создания CustomResourceDefinition
 - Написан yaml манифест для создания ServiceAccount с именем mysql-operator
 - Написан yaml манифест для создания для назначения роли ServiceAccount (clusterrole-full.yaml полный доступ, clusterrole-simple.yaml минимально необходимые права для работы ServiceAccount)
 - Написан yaml манифест для создания ClusterRoleBinding для привязки роли к имени ServiceAccount
 - Написан yaml манифест для создания CR - кастомного объекта kind: MySQL
 - Создана роль Ansible в директории mysql-app для задания **, которая выполняет следующие действия:
       - При создании в кластере объектов с типом MySQL (mysqls.otus.homework/v1) будет создавать deployment с заданным образом mysql, сервис типа ClusterIP, PV и PVC заданного размера 
       - При удалении объекта с типом MySQL будет удалять ранее созданные ресурсы
## ====
Роль корректно не отработала при попытке создать CR командой - kubectl apply -f config/samples/otus.homework_v1_mysql.yaml
Получаю ошибку, которую сохранил в файле ansible_execution_error.txt. Перепробовал разные варианты с с обновлением ansible-galaxy collection install kubernetes.core --upgrade, но это не дало никакого положительного результата.
## ====

## Как запустить проект:
 - Клонировать репозиторий на свою рабочую станцию и зайти в каталог kubernetes-operators
 - Выполнить команду для создания ServiceAccount - kubectl apply -f serviceaccount.yaml 
 - Выполнить команду для назначения роли ServiceAccount - kubectl apply -f clusterrole-full.yaml или kubectl apply -f clusterrole-simple.yaml (описание дано выше)
 - Выполнить команду для привязки роли к имени ServiceAccount - kubectl apply -f clusterrole-full.yaml
 - Выполнить команду для создания Deployment, который будет использоваться нашим оператором - kubectl apply -f deployment.yaml
 - Выполнить команду для создания CustomResourceDefinition - kubectl apply -f crd.yaml
 - Выполнить команду для создания CR - kubectl apply -f deployment.yaml
 - Выполнить команду для создания Service - kubectl apply -f mysql-cr.yaml
 
## Как проверить работоспособность:
- Проверить создание CRD - kubectl get crd mysqls.otus.homework (должна отобразиться наша CRD)
- Проверить создание деплоймента для оператора - kubectl get deployment mysql-app-deployment
- Проверить работу оператора можно командой - kubectl logs deployment/mysql-app-deployment
- Проверить создание кастомного ресурса - kubectl get mysqls.otus.homework или kubectl get mysql
- Проверить создание ресурсов для MySQL (Deployment, Service, PVC и PV) можно командами:
       - kubectl get deployments - должен отобразиться список деплойментов
       - kubectl get services - должен отобразиться сервис
       - kubectl get pvc - должен отобразиться PVC - mysql-app-cr-pvc, размер 10Gi
       - kubectl get pv - должен отобразиться PV - mysql-app-cr-pv, размер 10Gi
- При удалении кастомного ресурса MySQL командой - kubectl delete -f mysql-cr.yaml в логах kubectl logs -f deploy/mysql-app-deployment должна отобразиться информация Deletion is processed: 1 succeeded; 0 failed и при выполнении команд:
 kubectl get pvc, kubectl get pv, kubectl get services не должно остаться информации о нашем кастомном объекте - mysql-app-cr.

## PR checklist:
 - kubernetes-operators
