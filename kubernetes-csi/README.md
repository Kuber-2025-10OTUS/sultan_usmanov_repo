# Выполнено ДЗ № 12

 - Основное ДЗ - Развертывание системы хранения данных

## В процессе сделано:
 - Установил и настроил kubernetes кластер с одним хостом в Yandex Cloud
 - Создал s3 object storage в Yandex cloud
 - Создал ServiceAccount для доступа к бакету с правами storage.editor
 - Создал secret - s3_secret.yaml c ключами для доступа к Object Storage
 - Подготовил yaml манифест storageclass.yaml для создания StorageClass с именем csi-s3
 - Подготовил yaml манифест pvc.yaml для создания PersistentVolumeClaim
 - Подготовил yaml манифест pod.yaml для создания Pod внутри которого будет примонтирован S3 storage

## Как запустить проект:
 - Создать kubernetes кластер с одним хостом в Yandex Cloud
 - Создать s3 object storage в Yandex Cloud, я создал Bucket c именем "ycstorage" и размером 10ГБ
 - Создать ServiceAccount для доступа к бакету с правами storage.editor
 - Клонировать репозиторий на свою рабочую станцию, зайти в каталог kubernetes-csi
 - Выполнить команду для создания Secret - kubectl apply -f s3_secret.yaml
 - Выполнить команду для создания StorageClass - kubectl apply -f storageclass.yaml
 - Выполнить команды перечисленные ниже для работы с S3 object storage:
      * kubectl create -f provisioner.yaml
      * kubectl create -f driver.yaml
      * kubectl create -f csi-s3.yaml
 - Выполнить команду для создания PersistentVolumeClaim - kubectl apply -f pvc.yaml
 - Выполнить команду для создания Pod - kubectl apply -f pod.yaml
 
## Как проверить работоспособность:
 - Проверить создан ли StorageClass командой - kubectl get sc, должны получить ответ как в примере ниже:
      * NAME                           PROVISIONER                     RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
      * csi-s3                         ru.yandex.s3.csi                Delete          WaitForFirstConsumer   true                   27m
 - Проверить создан ли PersistentVolumeClaim командой - kubectl get pvc, должны получить ответ как в примере ниже:
      * NAME         STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
      * csi-s3-pvc   Bound    pvc-742bb1e3-5c42-4189-b390-0dd3dc9233bc   5Gi        RWX            csi-s3         <unset>                 133m
 - Проверить создан ли secret командой - kubectl get secret, должны получить ответ как в примере ниже:
      * NAME                           TYPE                 DATA   AGE
      * csi-s3-secret                  Opaque               1      3h47m
 - Проверить создался ли Pod командой - kubectl get pods, должны получить ответ как в примере ниже:
      * NAME                   READY   STATUS    RESTARTS   AGE
      * csi-s3-5x44f           2/2     Running   0          3h51m
      * csi-s3-provisioner-0   2/2     Running   0          3h51m
      * csi-s3-test-nginx      1/1     Running   0          153m
 - Проверить сохраняются ли файлы в ObjectStorage можно согласно описания ниже:
      * kubectl exec -ti csi-s3-test-nginx -- bash - подключаемся по SSH к нашему POD
      * cd /usr/share/nginx/html/s3 - переходим в директорию, которую примонтировали с S3
      * touch test.txt && echo "test" >> test.txt && cat test.txt - создаем файл в тестовых целях
      * Переходим в Yandex Cloud находим bucket - "ycstorage", должны увидеть что-то подобное - pvc-742bb1e3-5c42-4189-b390-0dd3dc9233bc/ и внутри, вы должны увидеть test.txt

## PR checklist:
 - kubernetes-csi
