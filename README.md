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
