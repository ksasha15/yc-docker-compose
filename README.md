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
root@u26:~/DevOps# docker build -t ksasha15/otus:users_api ./users-api/
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  558.1kB
Step 1/6 : FROM node:25-alpine3.22
25-alpine3.22: Pulling from library/node
1debc72441db: Pulling fs layer
d71a4048a969: Pulling fs layer
84f5eff04246: Pulling fs layer
75cfe5105822: Pulling fs layer
75cfe5105822: Download complete
b2eb3c6b65fd: Download complete
1debc72441db: Download complete
a4320d8c1e1d: Download complete
84f5eff04246: Download complete
84f5eff04246: Pull complete
d71a4048a969: Download complete
d71a4048a969: Pull complete
75cfe5105822: Pull complete
1debc72441db: Pull complete
Digest: sha256:d4f742bdaee57b81d8b1a1c78e210e5c3c2b3892b335a30575956819028c804e
Status: Downloaded newer image for node:25-alpine3.22
 ---> d4f742bdaee5
Step 2/6 : WORKDIR /app
 ---> Running in 4c8a457a588c
 ---> Removed intermediate container 4c8a457a588c
 ---> 66f25cf9955a
Step 3/6 : COPY . .
 ---> c4c083f506b7
Step 4/6 : RUN npm install && npm run build && rm -rf node_module
 ---> Running in 06b0b83e34cd
npm warn deprecated rimraf@3.0.2: Rimraf versions prior to v4 are no longer supported
npm warn deprecated inflight@1.0.6: This module is not supported, and leaks memory. Do not use it. Check out lru-cache if you want a good and tested way to coalesce async requests by a key value, which is much more comprehensive and powerful.
npm warn deprecated @humanwhocodes/object-schema@2.0.3: Use @eslint/object-schema instead
npm warn deprecated @humanwhocodes/config-array@0.11.14: Use @eslint/config-array instead
npm warn deprecated @hapi/joi@17.1.1: Switch to 'npm install joi'
npm warn deprecated @hapi/formula@2.0.0: Moved to 'npm install @sideway/formula'
npm warn deprecated @hapi/address@4.1.0: Moved to 'npm install @sideway/address'
npm warn deprecated glob@7.2.3: Glob versions prior to v9 are no longer supported
npm warn deprecated glob@7.2.3: Glob versions prior to v9 are no longer supported
npm warn deprecated glob@7.2.3: Glob versions prior to v9 are no longer supported
npm warn deprecated glob@7.2.3: Glob versions prior to v9 are no longer supported
npm warn deprecated glob@7.2.3: Glob versions prior to v9 are no longer supported

added 762 packages, and audited 763 packages in 42s

114 packages are looking for funding
  run `npm fund` for details

42 vulnerabilities (7 low, 17 moderate, 16 high, 2 critical)

To address issues that do not require attention, run:
  npm audit fix

To address all issues (including breaking changes), run:
  npm audit fix --force

Run `npm audit` for details.
npm notice
npm notice New minor version of npm available! 11.12.1 -> 11.16.0
npm notice Changelog: https://github.com/npm/cli/releases/tag/v11.16.0
npm notice To update run: npm install -g npm@11.16.0
npm notice

> users-api@0.0.1 build
> nest build

 ---> Removed intermediate container 06b0b83e34cd
 ---> 78ed2745dbcd
Step 5/6 : EXPOSE 3000
 ---> Running in 07ca12fab490
 ---> Removed intermediate container 07ca12fab490
 ---> 73ba05ce57b5
Step 6/6 : CMD ["npm", "run", "start"]
 ---> Running in 1538f3655fc2
 ---> Removed intermediate container 1538f3655fc2
 ---> 24eb8128599d
Successfully built 24eb8128599d
Successfully tagged ksasha15/otus:users_api
root@u26:~/DevOps#
root@u26:~/DevOps# docker run --name users_api -p 3000:3000 -dt --network net_users_api --network net_dashboard_ui ksasha15/otus:users_api
91f540621b8f01cb042b071523be3a284339fdce28d3f332e96954383984ffd6
root@u26:~/DevOps#docker build -t ksasha15/otus:documents_api ./documents-api/
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  560.6kB
Step 1/6 : FROM node:25-alpine3.22
 ---> d4f742bdaee5
Step 2/6 : WORKDIR /app
 ---> Using cache
 ---> 66f25cf9955a
Step 3/6 : COPY . .
 ---> cc2b4a42c0e0
Step 4/6 : RUN npm install && npm run build && rm -rf node_module
 ---> Running in a3d83c543521
npm warn deprecated rimraf@3.0.2: Rimraf versions prior to v4 are no longer supported
npm warn deprecated inflight@1.0.6: This module is not supported, and leaks memory. Do not use it. Check out lru-cache if you want a good and tested way to coalesce async requests by a key value, which is much more comprehensive and powerful.
npm warn deprecated @humanwhocodes/object-schema@2.0.3: Use @eslint/object-schema instead
npm warn deprecated @humanwhocodes/config-array@0.11.14: Use @eslint/config-array instead
npm warn deprecated @hapi/formula@2.0.0: Moved to 'npm install @sideway/formula'
npm warn deprecated @hapi/address@4.1.0: Moved to 'npm install @sideway/address'
npm warn deprecated @hapi/joi@17.1.1: Switch to 'npm install joi'
npm warn deprecated glob@7.2.3: Glob versions prior to v9 are no longer supported
npm warn deprecated glob@7.2.3: Glob versions prior to v9 are no longer supported
npm warn deprecated glob@7.2.3: Glob versions prior to v9 are no longer supported
npm warn deprecated glob@7.2.3: Glob versions prior to v9 are no longer supported
npm warn deprecated glob@7.2.3: Glob versions prior to v9 are no longer supported

added 763 packages, and audited 764 packages in 40s

114 packages are looking for funding
  run `npm fund` for details

42 vulnerabilities (7 low, 17 moderate, 16 high, 2 critical)

To address issues that do not require attention, run:
  npm audit fix

To address all issues (including breaking changes), run:
  npm audit fix --force

Run `npm audit` for details.
npm notice
npm notice New minor version of npm available! 11.12.1 -> 11.16.0
npm notice Changelog: https://github.com/npm/cli/releases/tag/v11.16.0
npm notice To update run: npm install -g npm@11.16.0
npm notice

> documents-api@0.0.1 build
> nest build

 ---> Removed intermediate container a3d83c543521
 ---> 58d7cb9bb574
Step 5/6 : EXPOSE 3000
 ---> Running in 4ef07a638a77
 ---> Removed intermediate container 4ef07a638a77
 ---> 4b14a5959381
Step 6/6 : CMD ["npm", "run", "start"]
 ---> Running in 4b569d45590d
 ---> Removed intermediate container 4b569d45590d
 ---> 4a486c0f0ee1
Successfully built 4a486c0f0ee1
Successfully tagged ksasha15/otus:documents_api
root@u26:~/DevOps# docker run --name documents_api -p 3002:3000 -dt --network net_documents_api --network net_dashboard_ui ksasha15/otus:documents_api
a56b57f53c18e802c888781d1afac8678dbb19be8c7ca70864408c362fbaf752
root@u26:~/DevOps# docker build -t ksasha15/otus:dashboard_ui ./dashboard-ui/
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  2.121MB
Step 1/6 : FROM node:16-alpine
16-alpine: Pulling from library/node
d9059661ce70: Pulling fs layer
7264a8db6415: Pulling fs layer
eee371b9ce3f: Pulling fs layer
93b3025fe103: Pulling fs layer
7264a8db6415: Download complete
93b3025fe103: Download complete
d9059661ce70: Download complete
7264a8db6415: Pull complete
eee371b9ce3f: Download complete
eee371b9ce3f: Pull complete
93b3025fe103: Pull complete
d9059661ce70: Pull complete
Digest: sha256:a1f9d027912b58a7c75be7716c97cfbc6d3099f3a97ed84aa490be9dee20e787
Status: Downloaded newer image for node:16-alpine
 ---> a1f9d027912b
Step 2/6 : WORKDIR /app
 ---> Running in fce348c62ffa
 ---> Removed intermediate container fce348c62ffa
 ---> 30bdefdc7fb9
Step 3/6 : COPY . .
 ---> 2bf7cdc8dee1
Step 4/6 : RUN yarn install && yarn build && rm -rf node_module
 ---> Running in 70822ae616ec
yarn install v1.22.19
warning package-lock.json found. Your project contains lock files generated by tools other than Yarn. It is advised not to mix package managers in order to avoid resolution inconsistencies caused by unsynchronized lock files. To clear this warning, remove package-lock.json.
[1/5] Validating package.json...
[2/5] Resolving packages...
[3/5] Fetching packages...
[4/5] Linking dependencies...
warning " > @testing-library/user-event@12.8.3" has unmet peer dependency "@testing-library/dom@>=7.21.4".
warning " > bootstrap@4.6.2" has unmet peer dependency "jquery@1.9.1 - 3".
warning " > bootstrap@4.6.2" has unmet peer dependency "popper.js@^1.16.1".
warning "react-scripts > @typescript-eslint/eslint-plugin > tsutils@3.21.0" has unmet peer dependency "typescript@>=2.8.0 || >= 3.2.0-dev || >= 3.3.0-dev || >= 3.4.0-dev || >= 3.5.0-dev || >= 3.6.0-dev || >= 3.6.0-beta || >= 3.7.0-dev || >= 3.7.0-beta".
[5/5] Building fresh packages...
Done in 122.71s.
yarn run v1.22.19
$ react-dotenv && react-scripts build
Creating an optimized production build...
Browserslist: caniuse-lite is outdated. Please run:
  npx update-browserslist-db@latest
  Why you should do it regularly: https://github.com/browserslist/update-db#readme
One of your dependencies, babel-preset-react-app, is importing the
"@babel/plugin-proposal-private-property-in-object" package without
declaring it in its dependencies. This is currently working because
"@babel/plugin-proposal-private-property-in-object" is already in your
node_modules folder for unrelated reasons, but it may break at any time.

babel-preset-react-app is part of the create-react-app project, which
is not maintianed anymore. It is thus unlikely that this bug will
ever be fixed. Add "@babel/plugin-proposal-private-property-in-object" to
your devDependencies to work around this error. This will make this message
go away.

Compiled successfully.

File sizes after gzip:

  507.04 KB  build/static/js/2.e8fa35cd.chunk.js
  35.05 KB   build/static/css/2.acc5d8fa.chunk.css
  2.85 KB    build/static/js/main.31095172.chunk.js
  778 B      build/static/js/runtime-main.c72c916a.js
  157 B      build/static/css/main.6ecc6c8a.chunk.css

The project was built assuming it is hosted at /.
You can control this with the homepage field in your package.json.

The build folder is ready to be deployed.
You may serve it with a static server:

  yarn global add serve
  serve -s build

Find out more about deployment here:

  https://cra.link/deployment

Done in 31.09s.

 ---> Removed intermediate container 70822ae616ec
 ---> 25cb42621ea0
Step 5/6 : EXPOSE 3000
 ---> Running in 57dbf31d9cf6
 ---> Removed intermediate container 57dbf31d9cf6
 ---> 3589da546442
Step 6/6 : CMD ["yarn", "start"]
 ---> Running in 8f52c3cfde41
 ---> Removed intermediate container 8f52c3cfde41
 ---> abc326b5d5b8
Successfully built abc326b5d5b8
Successfully tagged ksasha15/otus:dashboard_ui
root@u26:~/DevOps#
root@u26:~/DevOps# docker run --name dashboard_ui -p 3001:3000 -dt --network net_dashboard_ui ksasha15/otus:dashboard_ui
b964309326ec189a7817af4e85c75079a56502174d328650a0cb489fc0d3e3fb
root@u26:~/DevOps# docker images
                                                                                                 i Info →   U  In Use
IMAGE                                                                ID             DISK USAGE   CONTENT SIZE   EXTRA
alpine:latest                                                        5b10f432ef3d       13.1MB         3.95MB
ksasha15/otus:dashboard_ui                                           abc326b5d5b8        1.1GB          189MB    U
ksasha15/otus:documents_api                                          4a486c0f0ee1        641MB          136MB    U
ksasha15/otus:users_api                                              24eb8128599d        641MB          136MB    U
node:16-alpine                                                       a1f9d027912b        171MB         42.4MB
node:25-alpine3.22                                                   d4f742bdaee5        238MB         60.1MB
postgres:18                                                          8ff36f3c6637        650MB          168MB    U
registry.gitlab.com/gitlab-org/gitlab-runner/gitlab-runner-helper:x86_64-v18.11.3
                                                                     571952e633d3        134MB         37.3MB
root@u26:~/DevOps# docker ps -a
CONTAINER ID   IMAGE                         COMMAND                  CREATED          STATUS                      PORTS                                         NAMES
b964309326ec   ksasha15/otus:dashboard_ui    "docker-entrypoint.s…"   35 seconds ago   Up 35 seconds               0.0.0.0:3001->3000/tcp, [::]:3001->3000/tcp   dashboard_ui
a56b57f53c18   ksasha15/otus:documents_api   "docker-entrypoint.s…"   8 minutes ago    Exited (1) 8 minutes ago                                                  documents_api
91f540621b8f   ksasha15/otus:users_api       "docker-entrypoint.s…"   10 minutes ago   Exited (1) 10 minutes ago                                                 users_api
45052390a36b   postgres:18                   "docker-entrypoint.s…"   27 minutes ago   Up 27 minutes               0.0.0.0:5433->5432/tcp, [::]:5433->5432/tcp   pg_documents_api
62d9edf062da   postgres:18                   "docker-entrypoint.s…"   28 minutes ago   Up 28 minutes               0.0.0.0:5432->5432/tcp, [::]:5432->5432/tcp   pg_users_api
root@u26:~/DevOps# docker ps
CONTAINER ID   IMAGE                        COMMAND                  CREATED          STATUS          PORTS                                         NAMES
b964309326ec   ksasha15/otus:dashboard_ui   "docker-entrypoint.s…"   45 seconds ago   Up 45 seconds   0.0.0.0:3001->3000/tcp, [::]:3001->3000/tcp   dashboard_ui
45052390a36b   postgres:18                  "docker-entrypoint.s…"   28 minutes ago   Up 28 minutes   0.0.0.0:5433->5432/tcp, [::]:5433->5432/tcp   pg_documents_api
62d9edf062da   postgres:18                  "docker-entrypoint.s…"   28 minutes ago   Up 28 minutes   0.0.0.0:5432->5432/tcp, [::]:5432->5432/tcp   pg_users_api
root@u26:~/DevOps#

root@u26:~# docker login -u ksasha15

i Info → A Personal Access Token (PAT) can be used instead.
         To create a PAT, visit https://app.docker.com/settings


Password:

WARNING! Your credentials are stored unencrypted in '/root/.docker/config.json'.
Configure a credential helper to remove this warning. See
https://docs.docker.com/go/credential-store/

Login Succeeded
root@u26:~#
root@u26:~# docker push ksasha15/otus:dashboard_ui
The push refers to repository [docker.io/ksasha15/otus]
d9059661ce70: Mounted from library/node
43384e29c3b9: Pushed
8880b15efea4: Pushed
eee371b9ce3f: Mounted from library/node
7264a8db6415: Mounted from library/node
d7aa67aeab30: Pushed
93b3025fe103: Mounted from library/node
dashboard_ui: digest: sha256:abc326b5d5b876132c9baa64a19b28f08cdbd2ec65b42a9aa51d3b8b7c3dd57c size: 1863
root@u26:~# docker push ksasha15/otus:documents_api
The push refers to repository [docker.io/ksasha15/otus]
b181b62af144: Pushed
7fe29eb4eb70: Pushed
84f5eff04246: Mounted from library/node
d71a4048a969: Mounted from library/node
75cfe5105822: Mounted from library/node
1debc72441db: Mounted from library/node
c60784f82b08: Pushed
documents_api: digest: sha256:4a486c0f0ee135b36baacbbbb2cfc943a8f68ba0c1bddef6b074b99f8c137a84 size: 1837
root@u26:~# docker push ksasha15/otus:users_api
The push refers to repository [docker.io/ksasha15/otus]
c60784f82b08: Layer already exists
1bb7dd8cab56: Pushed
75cfe5105822: Layer already exists
1debc72441db: Layer already exists
302639d0081e: Pushed
84f5eff04246: Layer already exists
d71a4048a969: Layer already exists
users_api: digest: sha256:24eb8128599d56093c11106e5f675936c56df214d2e336f514996dcf943b3208 size: 1837
root@u26:~#
root@u26:~/DevOps# ssh -l ubuntu 111.88.144.158
The authenticity of host '111.88.144.158 (111.88.144.158)' can't be established.
ED25519 key fingerprint is: SHA256:yBzXh2T/lRGG/QzF0iSzPMxJORaJRdPz3QB+kYx4wSo
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '111.88.144.158' (ED25519) to the list of known hosts.
Welcome to Ubuntu 24.04.4 LTS (GNU/Linux 6.8.0-117-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Thu May 28 20:07:55 UTC 2026

  System load:  0.4                Processes:             105
  Usage of /:   11.4% of 18.72GB   Users logged in:       0
  Memory usage: 10%                IPv4 address for eth0: 10.129.0.34
  Swap usage:   0%


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

ubuntu@compute-vm-2-2-20-ssd-1779998418789:~$

root@compute-vm-2-2-20-ssd-1779998418789:~# docker-compose up -d
Starting root_pg_users_api_1     ... done
Starting root_dashboard_ui_1     ... done
Starting root_pg_documents_api_1 ... done
Starting root_documents_api_1    ... done
Starting root_users_api_1        ... done
root@compute-vm-2-2-20-ssd-1779998418789:~#
root@compute-vm-2-2-20-ssd-1779998418789:~# docker ps
CONTAINER ID   IMAGE                         COMMAND                  CREATED         STATUS                    PORTS                                         NAMES
8c7e527eb125   ksasha15/otus:documents_api   "docker-entrypoint.s…"   7 minutes ago   Up 11 seconds             0.0.0.0:3002->3000/tcp, [::]:3002->3000/tcp   root_documents_api_1
eb99799a5e7a   ksasha15/otus:users_api       "docker-entrypoint.s…"   7 minutes ago   Up 11 seconds             0.0.0.0:3000->3000/tcp, [::]:3000->3000/tcp   root_users_api_1
464a20c3f5f0   postgres:18                   "docker-entrypoint.s…"   8 minutes ago   Up 17 seconds (healthy)   0.0.0.0:5433->5432/tcp, [::]:5433->5432/tcp   root_pg_documents_api_1
360d8b811411   postgres:18                   "docker-entrypoint.s…"   8 minutes ago   Up 17 seconds (healthy)   0.0.0.0:5432->5432/tcp, [::]:5432->5432/tcp   root_pg_users_api_1
67fb7ec79f59   ksasha15/otus:dashboard_ui    "docker-entrypoint.s…"   8 minutes ago   Up 17 seconds             0.0.0.0:3001->3000/tcp, [::]:3001->3000/tcp   root_dashboard_ui_1
root@compute-vm-2-2-20-ssd-1779998418789:~#
```
