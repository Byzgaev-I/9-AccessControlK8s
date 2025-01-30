# Домашнее задание к занятию «Управление доступом»

## Цель задания

В тестовой среде Kubernetes нужно предоставить ограниченный доступ пользователю.

## Чеклист готовности к домашнему заданию

1. Установленное K8s-решение (MicroK8s)
2. Установленный локальный kubectl
3. Редактор YAML-файлов с подключенным GitHub-репозиторием

## Задание 1: Создать Deployment приложения и решить возникшую проблему с помощью ConfigMap.  

1) Создайте и подпишите SSL-сертификат для подключения к кластеру.
2) Настройте конфигурационный файл kubectl для подключения.
3) Создайте роли и все необходимые настройки для пользователя.
4) Предусмотрите права пользователя. Пользователь может просматривать логи подов и их конфигурацию (kubectl logs pod <pod_id>, kubectl describe pod <pod_id>).
5) Предоставьте манифесты и скриншоты и/или вывод необходимых команд.

### Решение

1. Проверка работоспособности MicroK8S:
```bash
microk8s status
```
![image](https://github.com/Byzgaev-I/9-AccessControlK8s/blob/main/1-1%20MicroK8S%20работает.png)

## Задание 1: Создание конфигурации пользователя
### 1. Создание SSL-сертификата

1) Создаем закрытый ключ:

```bash
openssl genrsa -out user1.key 2048
```

2) Создаем запрос на подпись сертификата (CSR):

```bash
openssl req -new -key user1.key -out user1.csr -subj "/CN=user1/O=group1"
```

3) Создаем конфигурационный файл для сертификата:
   
```bash
nano csr.conf
```
Содержимое csr.conf:

```text
[ req ]
default_bits = 2048
prompt = no
default_md = sha256
distinguished_name = dn

[ dn ]
CN = user1
O = group1

[ v3_ext ]
authorityKeyIdentifier=keyid,issuer:always
basicConstraints=CA:FALSE
keyUsage=keyEncipherment,dataEncipherment
extendedKeyUsage=clientAuth
```

4) Подписываем сертификат:

```bash
   sudo openssl x509 -req -in user1.csr \
    -CA /var/snap/microk8s/current/certs/ca.crt \
    -CAkey /var/snap/microk8s/current/certs/ca.key \
    -CAcreateserial \
    -out user1.crt -days 365 \
    -extfile csr.conf -extensions v3_ext
```

![image](https://github.com/Byzgaev-I/9-AccessControlK8s/blob/main/1-2%20подписываем%20сертификат.png)


## 2. Настройка kubectl

1) Создал тестовый Pod для проверки прав с содержимым test-pod.yaml:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  containers:
  - name: nginx
    image: nginx
```
и применил манифест:

```bash
kubectl apply -f test-pod.yaml
```

2) Настраиваем конфигурацию для нового пользователя:

   ```bash
kubectl config set-credentials user1 \
    --client-certificate=user1.crt \
    --client-key=user1.key

kubectl config set-context user1-context \
    --cluster=microk8s-cluster \
    --namespace=default \
    --user=user1
```

![image](https://github.com/Byzgaev-I/9-AccessControlK8s/blob/main/1-3%20настройка%20kubctl.png)



























