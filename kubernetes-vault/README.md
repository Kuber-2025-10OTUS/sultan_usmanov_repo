# Выполнено ДЗ № 11

 - Основное ДЗ - Хранилище секретов для приложения

## В процессе сделано:
 - Написан yaml манифест sa.yaml для создания сервисной учетной записи vault-auth и настройки роли.
 - Написан yaml манифест secretstore.yaml для создания SecretStore в namespace vault
 - Написан yaml манифест externalsecret-otus.yaml для создания ExternalSecret в namespace vault
 - Скачен и подготовлен HELM чарт для установки consul
 - Скачен и подготовлен HELM чарт для установки vault
 - Скачен и подготовлен HELM чарт для установки external-secrets
 - Подготовлена политика для vault для секретов /otus/cred - otus_vault_ro.hcl

## Как запустить проект:
 - Добавить репозиторий из которого планируем установить нужные нам компоненты:
   * helm repo add hashicorp https://helm.releases.hashicorp.com
   * helm repo add external-secrets	https://charts.external-secrets.io

 - Просмотреть список компонентов:
   * helm search repo hashicorp
   * helm search repo external-secrets 

 - Выкачать чарты и в дальнейшем работать с ними:
   * helm pull hashicorp/consul --untar
   * helm pull hashicorp/vault --untar
   * helm pull external-secrets/external-secrets --untar
   
 - Получить values файлы:
   * helm show values hashicorp/consul > consul_values.yaml
   * helm show values hashicorp/vault > vault_values.yaml
   * helm show values external-secrets/external-secrets > external_secrets_values.yaml

 - Установка компонентов:
   * helm install consul consul/ -f consul_values.yaml -n consul
   * helm install vault vault/ -n vault -f vault_values.yaml
   * external-secrets будет установлен отдельной командой

 - Инициализировать и распечатать vault в каждом поде vault:
   * vault operator unseal

 - Настроить перенаправление портов выполнив команду - kubectl port-forward service/vault 8200:8200 -n vault, после зайти в UI и выполнить:
   * Создайть хранилище секретов otus/ с Secret Engine KV, 
   * В нем создать секрет otus/cred, содержащий username='otus' password='asajkjkahs’

 - Создать сервисную учетную запись vault-auth выполнив команду - kubectl apply -f sa.yaml

 - В vault-0 выполнить команды согласно описания ниже:
   * vault login                                       # Залогиниться
   * vault auth enable kubernetes                      # Включить авторизацию kubernetes
   * vault policy write otus-policy otus_vault_ro.hcl  # Создать политику otus-policy
   * vault write auth/kubernetes/role/otus \           # Создать роль otus
        bound_service_account_names=vault-auth \
        bound_service_account_namespaces=vault \
        policies=otus-policy \
        ttl=24h \
        audience="https://kubernetes.default.svc.cluster.local"

 - Создать объект SecretStore выполнив команду - kubectl apply -f secretstore.yaml

 - Установить external-secrets выполнив команду - helm install external-secrets external-secrets/ -n vault -f external_secrets_values.yaml --set installCRDs=true

 - Создать объект ExternalSecret выполнив команду - kubectl apply -f externalsecret-otus.yaml

## Как проверить работоспособность:
 - Проверить статус запущенных подов: vault, external-secrets - kubectl get all -n vault
 - Проверить статус запущенных подов consul                   - kubectl get all -n consul
 - Проверить наличие secret d namespace vault                 - kubectl get secret otus-cred -n vault -o yaml
 - Проверить статус секрета - kubectl get externalsecret otus-cred -n vault -o yaml | grep -A5 status #Вывод команд приложил в файле get_secret_status.txt
 - Проверить содержимое username и password
    * kubectl get secret otus-cred -n vault -o jsonpath='{.data.username}' | base64 --decode
    * kubectl get secret otus-cred -n vault -o jsonpath='{.data.password}' | base64 --decode
 
## PR checklist:
 - kubernetes-vault