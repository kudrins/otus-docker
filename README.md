# Docker

Среда выполнения ДЗ: VM Centos 8 в VMware vSphere 7

### 1. Установить Docker, Docker Compose на хост.

Инструкция: <https://docs.docker.com/engine/install/centos/>

Команды:
```
#yum install -y yum-utils
#yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
#yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
#systemctl start docker
#systemctl enable docker

Проверка: #docker run hello-world
```
### 2. Создать свой кастомный образ nginx на базе alpine. 
После запуска nginx должен отдавать кастомную страницу.  
```
#mkdir /opt/docker-nginx
#cd /opt/docker-nginx

Создаем в каталоге файлы: Dockerfile, nginx.conf

#docker build -t kudrin_s/nginx:v3
#docker run -d -p 80:80 kudrin_s/nginx:v3
#docker ps
CONTAINER ID   IMAGE               COMMAND                  CREATED      STATUS          PORTS                NAMES
51a647af373b   kudrin_s/nginx:v3   "nginx -g 'daemon of…"   3 days ago   Up 17 seconds   0.0.0.0:80->80/tcp   lucid_turing
``` 
Загрузка образа в docker hub
```
[root@centos-docker ~]# docker images
REPOSITORY                       TAG       IMAGE ID       CREATED         SIZE
kudrin_s/nginx                   v3        670c0cf8c1a4   3 days ago      55.7MB
...
[root@centos-docker ~]# docker login --username kudrinsn
Password:
Login Succeeded

[root@centos-docker ~]# docker tag 670 kudrinsn/kudrinsn:nginx

[root@centos-docker ~]# docker push kudrinsn/kudrinsn:nginx
The push refers to repository [docker.io/kudrinsn/kudrinsn]
894249c8df89: Pushed
b2aab941d55b: Pushed
d4fc045c9e3a: Mounted from library/alpine
nginx: digest: sha256:d92de5e10f5863b79338e66481d6cbd6a38d394d21fe7b65d4edc749d0bd6b6f size: 947
```
загрузить образ из Docker Hub репозитория:  
```
docker pull kudrinsn/kudrinsn:nginx
```  
Браузер: http://10.100.11.106/  
`Default web page`

Скриншот в каталоге: screenshot

### 3. Определить разницу между контейнером и образом.
__Контейнер__  
Один Docker-контейнер = 1 сервис, сами контейнеры создаются из образов image. Так же можно внести изменения в работающем контенере, потом закоммитить его и получить новый образ.
```
docker commit 51a647af373b kudrin_s/nginx:v3.1
```
__Образ__  
Контейнеры создаются из образов, образ используется в качестве шаблона для создания контейнеров. Несколько контейнеров могут быть запущены, используя один и тот же образ.
Сам по себе Docker образ невозможно «запускать», запускаются контейнеры, внутри которых работает приложение. Образы можно хранить в `Docker` `Repository`, например, `Docker Hub` или `GitLab`, откуда образы можно загрузить на хостовую систему.  
Образ собирается из:  
  1) слоев, каждый слой собирается из своей директивы в Dockerfile'е, где директивы `ENV` и `ARG` не являются слоями и  
  2) начального образа, болванки, из директивы FROM в Dockerfile'е.  

Слои внутри образа `R/O` только на чтение, слои образа идемпотентны, это упрощает сборку образа из `Dockerfile`'ов. Например,  когда необходимо внести изменения только в один слой, изменить 1'у директиву в `Dockerfile`'е, и пересобрать образ, слои которые не правились будут использованы повторно в сборке `build`, такая сборка образа пройдет быстрее её первоначальной сборки.

### 4. Можно ли в контейнере собрать ядро?  
В Docker контейнере можно собрать ядро с произвольными патчами, флагами конфигурации и тегом, например, `repository` на сайте `Docker Hub` для сборки ядра `Debian` по ссылкам:  
hub https://hub.docker.com/r/tomzo/buildkernel  
git https://github.com/tomzo/docker-kernel-ide  
  
### 5. Задание со * (звездочкой) 

Каталог с решением [`docker_compose_redmine`]
```
Установка темы:
https://www.redmine.org/projects/redmine/wiki/Theme_List

Выбираем: https://github.com/mrliptontea/PurpleMine2?tab=readme-ov-file

Инстукция по установке темы на этой же странице

docker compose up
Ctrl+C
docker compose start
```
Браузер: http://10.100.11.106/ 
 
Скриншот в каталоге: screenshot