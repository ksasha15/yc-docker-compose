# yc-docker-compose

#### Описание выполнения домашнего задания:
Написать docker-compose для приложения в  GitLab.
В ReadMe описание запуска docker-compose, в виде готовых команд.
Запустить приложение на Yandex.Cloud через docker-compose.

Ниже код работы:
```
root@u26:~/DevOps#  git clone git@otusteam.gitlab.yandexcloud.net:devops/devops-2026-01/kas/users-api.git
Cloning into 'users-api'...
git@otusteam.gitlab.yandexcloud.net: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
root@u26:~/DevOps# 
root@u26:~/DevOps#  ssh -V
OpenSSH_9.6p1 Ubuntu-3ubuntu13.15, OpenSSL 3.0.13 30 Jan 2024
root@u26:~/DevOps#  ssh-keygen -t ed25519 -C "root@testpc"
Generating public/private ed25519 key pair.
Enter file in which to save the key (/root/.ssh/id_ed25519):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /root/.ssh/id_ed25519
Your public key has been saved in /root/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:tY9XycpJCTSgzEE49lHOyJ+SMan+gLIdutX8Fbr6Dmk root@testpc
The key's randomart image is:
+--[ED25519 256]--+
|     oo.o.o      |
|    ++.B . .     |
|   . oX.o o      |
|     ..= o o o . |
|    . o S . o +  |
|   = . o . = +   |
|. + E . . . *    |
| * o = o   .     |
|+.. .+*          |
+----[SHA256]-----+
root@u26:~/DevOps#  ll ../.ssh/
id_ed25519      id_ed25519.pub  known_hosts

<Прописываем этот ключ в Gitlab instance>

root@u26:~/DevOps#  git clone git@otusteam.gitlab.yandexcloud.net:devops/devops-2026-01/kas/users-api.git
Cloning into 'users-api'...
remote: Enumerating objects: 105, done.
remote: Counting objects: 100% (3/3), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 105 (delta 0), reused 0 (delta 0), pack-reused 102 (from 1)
Receiving objects: 100% (105/105), 103.34 KiB | 313.00 KiB/s, done.
Resolving deltas: 100% (45/45), done.
root@u26:~/DevOps#  git clone git@otusteam.gitlab.yandexcloud.net:devops/devops-2026-01/kas/documents-api.git
Cloning into 'documents-api'...
remote: Enumerating objects: 131, done.
remote: Counting objects: 100% (3/3), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 131 (delta 0), reused 0 (delta 0), pack-reused 128 (from 1)
Receiving objects: 100% (131/131), 102.92 KiB | 303.00 KiB/s, done.
Resolving deltas: 100% (65/65), done.
root@u26:~/DevOps#  git clone git@otusteam.gitlab.yandexcloud.net:devops/devops-2026-01/kas/dashboard-ui.git
Cloning into 'dashboard-ui'...
remote: Enumerating objects: 179, done.
remote: Total 179 (delta 0), reused 0 (delta 0), pack-reused 179 (from 1)
Receiving objects: 100% (179/179), 628.21 KiB | 360.00 KiB/s, done.
Resolving deltas: 100% (99/99), done.
root@u26:~/DevOps#  ll
total 0
drwxr-xr-x 1 root root 4096 May 28 06:06 ./
drwx------ 1 root root 4096 May 28 06:00 ../
drwxr-xr-x 1 root root 4096 May 23 12:49 GitLab/
drwxr-xr-x 1 root root 4096 May 28 06:07 dashboard-ui/
drwxr-xr-x 1 root root 4096 May 28 06:06 documents-api/
drwxr-xr-x 1 root root 4096 May 28 06:01 users-api/
root@u26:~/DevOps# 
root@u26:~/DevOps#  cat << EOF > users-api/Dockerfile
> FROM node:25-alpine3.22
> WORKDIR /app
> COPY . .
> RUN npm install && npm run build && rm -rf node_module
> EXPOSE 3000
> CMD ["npm", "run", "start"]
> EOF
root@u26:~/DevOps#  cat << EOF > documents-api/Dockerfile
> FROM node:25-alpine3.22
> WORKDIR /app
> COPY . .
> RUN npm install && npm run build && rm -rf node_module
> EXPOSE 3000
> CMD ["npm", "run", "start"]
> EOF
root@u26:~/DevOps# cat << EOF > dashboard-ui/Dockerfile
> FROM node:16-alpine
> WORKDIR /app
> COPY . .
> RUN yarn install && yarn build && rm -rf node_module
> EXPOSE 3000
> CMD ["yarn", "start"]
> EOF
root@u26:~/DevOps# 
root@u26:~/DevOps# docker network create net_users_api
root@u26:~/DevOps# docker network create net_documents_api
root@u26:~/DevOps# docker network create net_dashboard_ui
e495d50cba99949d270e1dd7dbc9f4ffa4f9dcf3349736e370118746f0145ad2
853bbbcd154e9ab1790afec081bbc9a46fede587c97bf6ae6beffc8b52ecc4df
874d716b393c91744cc7031e84edacad437a213a833cb809bc4a68ac7395d9ad
root@u26:~/DevOps#
root@u26:~/DevOps# docker run --name pg_users_api -p 5432:5432 -e POSTGRES_USER=garantme -e POSTGRES_PASSWORD=yours_db_password  -e POSTGRES_DB=otus_users_db -v ./pg_user_api_data:/var/lib/postgresql -dt --network net_users_api postgres:18
62d9edf062da4cdf3d3b5ebd499e13b081541de8642f8ebda998a248ca153edb
root@u26:~/DevOps# docker run --name pg_documents_api -p 5433:5432 -e POSTGRES_USER=garantme -e POSTGRES_PASSWORD=yours_db_password  -e POSTGRES_DB=otus_documents_db -v ./pg_documents_api_data:/var/lib/postgresql -dt --network net_documents_api postgres:18
45052390a36b8346dc57f38c74a50aca71650ba513a851289ed5259b22e7e143
root@u26:~/DevOps# ll
total 28
drwxr-xr-x 7 root root 4096 May 27 20:39 ./
drwx------ 8 root root 4096 May 27 20:26 ../
drwxr-xr-x 5 root root 4096 May 27 20:29 dashboard-ui/
drwxr-xr-x 5 root root 4096 May 27 20:29 documents-api/
drwxr-xr-x 3 root root 4096 May 27 20:39 pg_documents_api_data/
drwxr-xr-x 3 root root 4096 May 27 20:39 pg_user_api_data/
drwxr-xr-x 5 root root 4096 May 27 20:28 users-api/
root@u26:~/DevOps#
root@u26:~/DevOps# docker ps
CONTAINER ID   IMAGE         COMMAND                  CREATED         STATUS         PORTS                                         NAMES
45052390a36b   postgres:18   "docker-entrypoint.s…"   7 minutes ago   Up 6 minutes   0.0.0.0:5433->5432/tcp, [::]:5433->5432/tcp   pg_documents_api
62d9edf062da   postgres:18   "docker-entrypoint.s…"   7 minutes ago   Up 7 minutes   0.0.0.0:5432->5432/tcp, [::]:5432->5432/tcp   pg_users_api
root@u26:~/DevOps#

```
