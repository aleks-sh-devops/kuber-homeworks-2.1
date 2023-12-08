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

Запускаем манифест, заходим в ПОД и проверяем доступность логов ноды куба:
```
usrcon@cli-k8s-01:~/manifests/05_dz_kuber_2.1$ kubectl apply -f ~/manifests/05_dz_kuber_2.1/03_dms_multitool.yml
daemonset.apps/logs-multitool created
usrcon@cli-k8s-01:~/manifests/05_dz_kuber_2.1$ kubectl get pods -n dz6
NAME                                     READY   STATUS    RESTARTS   AGE
app-multitool-busybox-7bf96cc9b6-zsvkt   2/2     Running   0          22m
logs-multitool-f98x7                     1/1     Running   0          5s
usrcon@cli-k8s-01:~/manifests/05_dz_kuber_2.1$
usrcon@cli-k8s-01:~/manifests/05_dz_kuber_2.1$ kubectl exec -n dz6 logs-multitool-f98x7 -it -- bash
logs-multitool-f98x7:/# ls -lah /
total 84K
drwxr-xr-x    1 root     root        4.0K Dec  8 20:24 .
drwxr-xr-x    1 root     root        4.0K Dec  8 20:24 ..
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 bin
drwx------    2 root     root        4.0K Sep 14 11:11 certs
drwxr-xr-x    5 root     root         360 Dec  8 20:24 dev
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 docker
drwxr-xr-x    1 root     root        4.0K Dec  8 20:24 etc
drwxr-xr-x    2 root     root        4.0K Aug  7 13:09 home
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 lib
drwxrwxr-x   11 root     113         4.0K Dec  3 00:00 log_data
drwxr-xr-x    5 root     root        4.0K Aug  7 13:09 media
drwxr-xr-x    2 root     root        4.0K Aug  7 13:09 mnt
drwxr-xr-x    2 root     root        4.0K Aug  7 13:09 opt
dr-xr-xr-x  291 root     root           0 Dec  8 20:24 proc
drwx------    2 root     root        4.0K Aug  7 13:09 root
drwxr-xr-x    1 root     root        4.0K Dec  8 20:24 run
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 sbin
drwxr-xr-x    2 root     root        4.0K Aug  7 13:09 srv
dr-xr-xr-x   13 root     root           0 Dec  8 20:24 sys
drwxrwxrwt    2 root     root        4.0K Aug  7 13:09 tmp
drwxr-xr-x    1 root     root        4.0K Aug  7 13:09 usr
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 var
logs-multitool-f98x7:/# ls -lah /log_data/
total 25M
drwxrwxr-x   11 root     113         4.0K Dec  3 00:00 .
drwxr-xr-x    1 root     root        4.0K Dec  8 20:24 ..
-rw-r--r--    1 root     root       34.5K Dec  8 13:34 alternatives.log
drwxr-xr-x    2 root     root        4.0K Dec  8 13:34 apt
-rw-r-----    1 107      adm        33.0K Dec  8 20:17 auth.log
-rw-r-----    1 107      adm        41.6K Dec  2 23:17 auth.log.1
-rw-r--r--    1 root     root       63.0K Aug 10 00:17 bootstrap.log
-rw-rw----    1 root     43             0 Aug 10 00:17 btmp
-rw-r-----    1 root     adm         7.0K Nov 30 19:40 cloud-init-output.log
-rw-r-----    1 107      adm       113.5K Nov 30 19:40 cloud-init.log
drwxr-xr-x    2 root     root       12.0K Dec  8 20:24 containers
drwxr-xr-x    2 root     root        4.0K Aug  2 15:53 dist-upgrade
-rw-r-----    1 root     adm        54.9K Nov 30 19:40 dmesg
-rw-r-----    1 root     adm        55.7K Nov 30 19:39 dmesg.0
-rw-r--r--    1 root     root      583.5K Dec  8 13:34 dpkg.log
-rw-r--r--    1 root     root       31.3K Nov 30 19:39 faillog
drwxr-x---    4 root     adm         4.0K Nov 30 19:31 installer
drwxr-sr-x    3 root     nginx       4.0K Nov 30 19:38 journal
-rw-r-----    1 107      adm         2.5K Dec  8 20:24 kern.log
-rw-r-----    1 107      adm       158.0K Dec  1 04:30 kern.log.1
drwxr-xr-x    2 111      117         4.0K Nov 30 19:40 landscape
-rw-rw-r--    1 root     43        285.4K Dec  5 10:59 lastlog
drwxr-xr-x   21 root     root        4.0K Dec  8 20:24 pods
drwx------    2 root     root        4.0K Aug 10 00:20 private
-rw-r-----    1 107      adm        16.1M Dec  8 20:26 syslog
-rw-r-----    1 107      adm         7.4M Dec  3 00:00 syslog.1
-rw-r--r--    1 root     root       21.5K Dec  8 03:37 ubuntu-advantage.log
drwxr-x---    2 root     adm         4.0K Nov 30 19:39 unattended-upgrades
-rw-rw-r--    1 root     43          3.8K Dec  5 10:59 wtmp
logs-multitool-f98x7:/#
logs-multitool-f98x7:/# tail /log_data/syslog
Dec  8 20:25:56 microk8s-03 microk8s.daemon-kubelite[743712]: I1208 20:25:56.399766  743712 handler.go:232] Adding GroupVersion crd.projectcalico.org v1 to ResourceManager
Dec  8 20:25:56 microk8s-03 microk8s.daemon-kubelite[743712]: I1208 20:25:56.400040  743712 handler.go:232] Adding GroupVersion crd.projectcalico.org v1 to ResourceManager
Dec  8 20:25:56 microk8s-03 microk8s.daemon-kubelite[743712]: I1208 20:25:56.400838  743712 handler.go:232] Adding GroupVersion crd.projectcalico.org v1 to ResourceManager
Dec  8 20:26:00 microk8s-03 systemd[1]: run-containerd-runc-k8s.io-380a585f5f2fbfd274fb1c3aa5fedb3ef5d5fefb359d3feaf4f4fe5c295caa2b-runc.8zgutl.mount: Deactivated successfully.
Dec  8 20:26:04 microk8s-03 systemd[1]: run-containerd-runc-k8s.io-2ed197ae890a8fa652c4b6e31f16e8b5ec173f5c2f0e300283be39a5f352f79d-runc.O5itWL.mount: Deactivated successfully.
Dec  8 20:26:10 microk8s-03 systemd[1]: run-containerd-runc-k8s.io-380a585f5f2fbfd274fb1c3aa5fedb3ef5d5fefb359d3feaf4f4fe5c295caa2b-runc.KKzTKe.mount: Deactivated successfully.
Dec  8 20:26:15 microk8s-03 systemd[1]: run-containerd-runc-k8s.io-2ed197ae890a8fa652c4b6e31f16e8b5ec173f5c2f0e300283be39a5f352f79d-runc.HcBysA.mount: Deactivated successfully.
Dec  8 20:26:20 microk8s-03 systemd[1]: run-containerd-runc-k8s.io-380a585f5f2fbfd274fb1c3aa5fedb3ef5d5fefb359d3feaf4f4fe5c295caa2b-runc.IKqaxX.mount: Deactivated successfully.
Dec  8 20:26:30 microk8s-03 systemd[1]: run-containerd-runc-k8s.io-380a585f5f2fbfd274fb1c3aa5fedb3ef5d5fefb359d3feaf4f4fe5c295caa2b-runc.aB6s2B.mount: Deactivated successfully.
Dec  8 20:26:48 microk8s-03 systemd[1]: run-containerd-runc-k8s.io-380a585f5f2fbfd274fb1c3aa5fedb3ef5d5fefb359d3feaf4f4fe5c295caa2b-runc.3ZNE31.mount: Deactivated successfully.
logs-multitool-f98x7:/# exit
```

Логи доступны. Успех!

------
