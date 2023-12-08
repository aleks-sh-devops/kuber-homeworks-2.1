# Домашнее задание к занятию «Хранение в K8s. Часть 1»

### Цель задания

В тестовой среде Kubernetes нужно обеспечить обмен файлами между контейнерам пода и доступ к логам ноды.

------

### Чеклист готовности к домашнему заданию

1. Установленное K8s-решение (например, MicroK8S).
2. Установленный локальный kubectl.
3. Редактор YAML-файлов с подключенным GitHub-репозиторием.

------

### Дополнительные материалы для выполнения задания

1. [Инструкция по установке MicroK8S](https://microk8s.io/docs/getting-started).
2. [Описание Volumes](https://kubernetes.io/docs/concepts/storage/volumes/).
3. [Описание Multitool](https://github.com/wbitt/Network-MultiTool).

------

### Задание 1 

**Что нужно сделать**

Создать Deployment приложения, состоящего из двух контейнеров и обменивающихся данными.

1. Создать Deployment приложения, состоящего из контейнеров busybox и multitool.
2. Сделать так, чтобы busybox писал каждые пять секунд в некий файл в общей директории.
3. Обеспечить возможность чтения файла контейнером multitool.
4. Продемонстрировать, что multitool может читать файл, который периодоически обновляется.
5. Предоставить манифесты Deployment в решении, а также скриншоты или вывод команды из п. 4.

Создаем пространство имен:  
```
usrcon@cli-k8s-01:~/manifests/05_dz_kuber_2.1$ kubectl apply -f ~/manifests/05_dz_kuber_2.1/01_namespace.yml
namespace/dz6 created
usrcon@cli-k8s-01:~/manifests/05_dz_kuber_2.1$ kubectl get ns
NAME              STATUS   AGE
kube-system       Active   7d23h
kube-public       Active   7d23h
kube-node-lease   Active   7d23h
default           Active   7d23h
lesson4           Active   7d23h
ingress           Active   7d23h
dz5               Active   3d12h
lesson5           Active   3d6h
dz6               Active   14s
```

Запускаю манифест и проверяю доступность общих файлов:
```
usrcon@cli-k8s-01:~/manifests/05_dz_kuber_2.1$ kubectl apply -f ~/manifests/05_dz_kuber_2.1/02_deploy_busybox_multitool.yml
deployment.apps/app-multitool-busybox created
usrcon@cli-k8s-01:~/manifests/05_dz_kuber_2.1$ kubectl get pods -n dz6
NAME                                     READY   STATUS    RESTARTS   AGE
app-multitool-busybox-7bf96cc9b6-zsvkt   2/2     Running   0          11s
kubectl exec -n dz6  app-multitool-busybox-7bf96cc9b6-zsvkt -c multitool -it -- bash
app-multitool-busybox-7bf96cc9b6-zsvkt:/# ls -lah /
total 84K
drwxr-xr-x    1 root     root        4.0K Dec  8 20:01 .
drwxr-xr-x    1 root     root        4.0K Dec  8 20:01 ..
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 bin
drwx------    2 root     root        4.0K Sep 14 11:11 certs
drwxr-xr-x    5 root     root         360 Dec  8 20:01 dev
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 docker
drwxr-xr-x    1 root     root        4.0K Dec  8 20:01 etc
drwxr-xr-x    2 root     root        4.0K Aug  7 13:09 home
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 lib
drwxr-xr-x    5 root     root        4.0K Aug  7 13:09 media
drwxr-xr-x    2 root     root        4.0K Aug  7 13:09 mnt
drwxrwxrwx    2 root     root        4.0K Dec  8 20:01 multitool_dir
drwxr-xr-x    2 root     root        4.0K Aug  7 13:09 opt
dr-xr-xr-x  289 root     root           0 Dec  8 20:01 proc
drwx------    2 root     root        4.0K Aug  7 13:09 root
drwxr-xr-x    1 root     root        4.0K Dec  8 20:01 run
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 sbin
drwxr-xr-x    2 root     root        4.0K Aug  7 13:09 srv
dr-xr-xr-x   13 root     root           0 Dec  8 20:01 sys
drwxrwxrwt    2 root     root        4.0K Aug  7 13:09 tmp
drwxr-xr-x    1 root     root        4.0K Aug  7 13:09 usr
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 var
app-multitool-busybox-7bf96cc9b6-zsvkt:/# ls -lah /multitool_dir/
total 12K
drwxrwxrwx    2 root     root        4.0K Dec  8 20:01 .
drwxr-xr-x    1 root     root        4.0K Dec  8 20:01 ..
-rw-r--r--    1 root     root        1.9K Dec  8 20:05 date.log
app-multitool-busybox-7bf96cc9b6-zsvkt:/# cat /multitool_dir/date.log
pinhhhh date = Fri Dec  8 20:01:59 UTC 2023
pinhhhh date = Fri Dec  8 20:02:04 UTC 2023
pinhhhh date = Fri Dec  8 20:02:09 UTC 2023
pinhhhh date = Fri Dec  8 20:02:14 UTC 2023
pinhhhh date = Fri Dec  8 20:02:19 UTC 2023
pinhhhh date = Fri Dec  8 20:02:24 UTC 2023
pinhhhh date = Fri Dec  8 20:02:29 UTC 2023
pinhhhh date = Fri Dec  8 20:02:34 UTC 2023
pinhhhh date = Fri Dec  8 20:02:39 UTC 2023
pinhhhh date = Fri Dec  8 20:02:44 UTC 2023
pinhhhh date = Fri Dec  8 20:02:49 UTC 2023
pinhhhh date = Fri Dec  8 20:02:54 UTC 2023
pinhhhh date = Fri Dec  8 20:02:59 UTC 2023
pinhhhh date = Fri Dec  8 20:03:04 UTC 2023
pinhhhh date = Fri Dec  8 20:03:09 UTC 2023
pinhhhh date = Fri Dec  8 20:03:14 UTC 2023
pinhhhh date = Fri Dec  8 20:03:19 UTC 2023
pinhhhh date = Fri Dec  8 20:03:24 UTC 2023
pinhhhh date = Fri Dec  8 20:03:29 UTC 2023
pinhhhh date = Fri Dec  8 20:03:34 UTC 2023
pinhhhh date = Fri Dec  8 20:03:39 UTC 2023
pinhhhh date = Fri Dec  8 20:03:44 UTC 2023
pinhhhh date = Fri Dec  8 20:03:49 UTC 2023
pinhhhh date = Fri Dec  8 20:03:54 UTC 2023
pinhhhh date = Fri Dec  8 20:03:59 UTC 2023
pinhhhh date = Fri Dec  8 20:04:04 UTC 2023
pinhhhh date = Fri Dec  8 20:04:09 UTC 2023
pinhhhh date = Fri Dec  8 20:04:14 UTC 2023
pinhhhh date = Fri Dec  8 20:04:19 UTC 2023
pinhhhh date = Fri Dec  8 20:04:24 UTC 2023
pinhhhh date = Fri Dec  8 20:04:29 UTC 2023
pinhhhh date = Fri Dec  8 20:04:34 UTC 2023
pinhhhh date = Fri Dec  8 20:04:39 UTC 2023
pinhhhh date = Fri Dec  8 20:04:44 UTC 2023
pinhhhh date = Fri Dec  8 20:04:49 UTC 2023
pinhhhh date = Fri Dec  8 20:04:54 UTC 2023
pinhhhh date = Fri Dec  8 20:04:59 UTC 2023
pinhhhh date = Fri Dec  8 20:05:04 UTC 2023
pinhhhh date = Fri Dec  8 20:05:09 UTC 2023
pinhhhh date = Fri Dec  8 20:05:14 UTC 2023
pinhhhh date = Fri Dec  8 20:05:19 UTC 2023
pinhhhh date = Fri Dec  8 20:05:24 UTC 2023
pinhhhh date = Fri Dec  8 20:05:29 UTC 2023
pinhhhh date = Fri Dec  8 20:05:34 UTC 2023
pinhhhh date = Fri Dec  8 20:05:39 UTC 2023
pinhhhh date = Fri Dec  8 20:05:44 UTC 2023
pinhhhh date = Fri Dec  8 20:05:49 UTC 2023
pinhhhh date = Fri Dec  8 20:05:54 UTC 2023
pinhhhh date = Fri Dec  8 20:05:59 UTC 2023
app-multitool-busybox-7bf96cc9b6-zsvkt:/# exit
exit
```

Успех!

------

### Задание 2

**Что нужно сделать**

Создать DaemonSet приложения, которое может прочитать логи ноды.

1. Создать DaemonSet приложения, состоящего из multitool.
2. Обеспечить возможность чтения файла `/var/log/syslog` кластера MicroK8S.
3. Продемонстрировать возможность чтения файла изнутри пода.
4. Предоставить манифесты Deployment, а также скриншоты или вывод команды из п. 2.

------
