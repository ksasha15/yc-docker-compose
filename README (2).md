# devops-docker-compose

#### Описание выполнения задания:
- Завести репозиторий в Docker Hub.
- Упаковать микросервисное приложение в Dockerfile
- Собрать образ из полученного Dockerfile
- Запушить в репозиторий образы из предыдущего шага.
- Настроить хранение данных приложения/бд в volume.
- Создать отдельную Docker-network для стэка приложения.
- Запустить приложение в контейнерах в Yandex Cloud.
Предоставить доступ к ВМ, для проверки доступности образов.

#### Репозиторий в Докер Хабе:
https://hub.docker.com/repository/docker/ksasha15/compose/general

#### Описание микросервисов
Основа взята из описания на Docker Docs. Все файлы проекта находятся в этом репозитории. Сервис считает количество обращений к сайту. Все настройки приложения, порты, сети, подключаемые тома согласно compose.yaml. Ниже привожу код работы:
```
root@Ubuntu22:/home/u24/Compose# ll
total 36
drwxrwxr-x  3 u24  u24  4096 Apr  5 10:34 ./
drwxr-x--- 23 u24  u24  4096 Apr  5 06:40 ../
-rw-rw-r--  1 u24  u24   321 Apr  5 08:05 app.py
-rw-rw-r--  1 u24  u24   718 Apr  5 09:52 compose.yaml
-rw-rw-r--  1 u24  u24   256 Apr  5 07:02 Dockerfile
-rw-rw-r--  1 u24  u24    34 Apr  5 06:38 .dockerignore
-rw-rw-r--  1 u24  u24    47 Apr  5 06:34 .env
drwxr-xr-x  8 root root 4096 Apr  5 10:40 .git/
-rw-rw-r--  1 u24  u24    12 Apr  5 07:49 requirements.txt
root@Ubuntu22:/home/u24/Compose# docker compose up -d
[+] up 3/3
 ✔ Network compose_app_network Created                                       0.2s
 ✔ Container compose-redis-1   Healthy                                       6.1s
 ✔ Container compose-web-1     Started                                       6.5s
root@Ubuntu22:/home/u24/Compose# curl localhost:8000
Hello from Compose Watch!! I have been seen 1 time(s).
root@Ubuntu22:/home/u24/Compose# curl localhost:8000
Hello from Compose Watch!! I have been seen 2 time(s).
root@Ubuntu22:/home/u24/Compose# curl localhost:8000
Hello from Compose Watch!! I have been seen 3 time(s).
root@Ubuntu22:/home/u24/Compose#
```
#### Доступ на YandexCloud
Использован код для bastion-хоста из предыдущего задания. 
```
u24@Ubuntu22:~/Compose$ ssh -i ../.ssh/ssh-key-1771855316608 yc-user@158.160.245.71
Welcome to Ubuntu 18.04.6 LTS (GNU/Linux 4.15.0-213-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

  System information as of Sun Apr  5 11:51:14 UTC 2026

  System load:  0.12              Processes:           92
  Usage of /:   63.5% of 2.26GB   Users logged in:     0
  Memory usage: 6%                IP address for eth0: 192.168.0.5
  Swap usage:   0%

 * Strictly confined Kubernetes makes edge and IoT secure. Learn how MicroK8s
   just raised the bar for easy, resilient and secure K8s cluster deployment.

   https://ubuntu.com/engage/secure-kubernetes-at-the-edge

Expanded Security Maintenance for Infrastructure is not enabled.

0 updates can be applied immediately.

148 additional security updates can be applied with ESM Infra.
Learn more about enabling ESM Infra service for Ubuntu 18.04 at
https://ubuntu.com/18-04



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

yc-user@fv4873f9frfmhv0or8i4:~$ sudo -i
root@fv4873f9frfmhv0or8i4:~# apt install -y docker-compose
root@fv4873f9frfmhv0or8i4:~# docker pull ksasha15/compose:web
web: Pulling from ksasha15/compose
589002ba0eae: Pull complete
6de7811bee64: Pull complete
6f3f43492c5b: Pull complete
89a8227ba99b: Pull complete
021869dff65d: Pull complete
891d1741712e: Pull complete
5b548185a8e9: Pull complete
db84942518e9: Pull complete
2dbd86322bf8: Pull complete
Digest: sha256:469671d03c4938fd8067b316cf054e4d564dd2ac06f4a3e6cb01f18a3db4d25e
Status: Downloaded newer image for ksasha15/compose:web
docker.io/ksasha15/compose:web
root@fv4873f9frfmhv0or8i4:~# docker run -tdi  ksasha15/compose:web
 * Serving Flask app 'app.py'
 * Debug mode: on
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:5000
 * Running on http://172.17.0.2:5000
Press CTRL+C to quit
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 122-018-903

root@fv4873f9frfmhv0or8i4:~# docker images
REPOSITORY         TAG            IMAGE ID       CREATED       SIZE
ksasha15/compose   web            cd2a842c609a   5 hours ago   272MB
ksasha15/compose   redis-alpine   b95f59534a91   11 days ago   97.4MB
root@fv4873f9frfmhv0or8i4:~# docker ps -a
CONTAINER ID   IMAGE                           COMMAND                  CREATED              STATUS                     PORTS      NAMES
fe05b9a735f5   ksasha15/compose:redis-alpine   "docker-entrypoint.s…"   28 seconds ago       Up 26 seconds              6379/tcp   confident_torvalds
9f16eb74661c   ksasha15/compose:web            "flask run --debug"      About a minute ago   Up About a minute          5000/tcp   blissful_maxwell
7c61caeb04ad   ksasha15/compose:web            "flask run --debug"      2 minutes ago        Exited (0) 2 minutes ago              adoring_archimedes
9fd46a48be21   ksasha15/compose:web            "-d"                     2 minutes ago        Created                    5000/tcp   wonderful_ride
32670e50241a   ksasha15/compose:web            "flask run --debug"      3 minutes ago        Exited (0) 3 minutes ago              epic_dewdney
root@fv4873f9frfmhv0or8i4:~# curl localhost:5000
curl: (7) Failed to connect to localhost port 5000: Connection refused
root@fv4873f9frfmhv0or8i4:~# docker run -tid -p 8000:5000 ksasha15/compose:web
77627edc06a34e4c10b77ff8d0389939da455990469d3d4d6ba39f1707b09a6a
root@fv4873f9frfmhv0or8i4:~# curl localhost:5000
curl: (7) Failed to connect to localhost port 5000: Connection refused
root@fv4873f9frfmhv0or8i4:~# docker ps -a
CONTAINER ID   IMAGE                           COMMAND                  CREATED         STATUS                     PORTS                                       NAMES
77627edc06a3   ksasha15/compose:web            "flask run --debug"      9 seconds ago   Up 8 seconds               0.0.0.0:8000->5000/tcp, :::8000->5000/tcp   suspicious_borg
fe05b9a735f5   ksasha15/compose:redis-alpine   "docker-entrypoint.s…"   4 minutes ago   Up 4 minutes               6379/tcp                                    confident_torvalds
9f16eb74661c   ksasha15/compose:web            "flask run --debug"      5 minutes ago   Up 5 minutes               5000/tcp                                    blissful_maxwell
7c61caeb04ad   ksasha15/compose:web            "flask run --debug"      5 minutes ago   Exited (0) 5 minutes ago                                               adoring_archimedes
9fd46a48be21   ksasha15/compose:web            "-d"                     6 minutes ago   Created                    5000/tcp                                    wonderful_ride
32670e50241a   ksasha15/compose:web            "flask run --debug"      7 minutes ago   Exited (0) 6 minutes ago                                               epic_dewdney
root@fv4873f9frfmhv0or8i4:~# ss -tlnp
State         Recv-Q         Send-Q                  Local Address:Port                    Peer Address:Port
LISTEN        0              128                           0.0.0.0:8000                         0.0.0.0:*             users:(("docker-proxy",pid=6030,fd=4))
LISTEN        0              128                         127.0.0.1:34613                        0.0.0.0:*             users:(("containerd",pid=2539,fd=14))
LISTEN        0              128                     127.0.0.53%lo:53                           0.0.0.0:*             users:(("systemd-resolve",pid=724,fd=13))
LISTEN        0              128                           0.0.0.0:22                           0.0.0.0:*             users:(("sshd",pid=959,fd=3))
LISTEN        0              128                              [::]:8000                            [::]:*             users:(("docker-proxy",pid=6037,fd=4))
LISTEN        0              128                              [::]:22                              [::]:*             users:(("sshd",pid=959,fd=4))
root@fv4873f9frfmhv0or8i4:~#  curl localhost:8000
Hello from Compose Watch!! I have been seen 1 time(s).
```
