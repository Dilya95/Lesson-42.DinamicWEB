# Домашнее задание 42: Dynamic WEB

## Задания
Варианты стенда:<br>

nginx + php-fpm (laravel/wordpress) + python (flask/django) + js(react/angular);<br>
nginx + java (tomcat/jetty/netty) + go + ruby;<br>
можно свои комбинации.<br>

Реализации на выбор:<br>

на хостовой системе через конфиги в /etc;<br>
деплой через docker-compose.<br>

Для усложнения можно попросить проекты у коллег с курсов по разработке<br>


К сдаче принимается:<br>

vagrant стэнд с проброшенными на локалхост портами<br>
каждый порт на свой сайт<br>
через нжинкс<br>



## Структура
    ├── README.md
    └── project
        ├── .env
        ├── docker-compose.yml
        ├── nginx-conf
        │   └── nginx.conf
        ├── node
        │   └── test.js
        ├── python
        │   ├── Dockerfile
        │   ├── manage.py
        │   ├── mysite
        │   │   ├── __init__.py
        │   │   ├── asgi.py
        │   │   ├── settings.py
        │   │   ├── urls.py
        │   │   └── wsgi.py
        │   └── requirements.txt
        └── screens
            ├── 8081.png
            ├── 8082.png
            └── 8083.png




## Выполнение

### Стенд: Nginx + WordPress (PHP-FPM) + Django (Gunicorn/WSGI) + Node.js
```
Разворачивала вручную на своём компьютере через Docker Desktop / docker-compose.
```


### Разворачивание контейнеров
```
dilyam@MacBook-Pro-Dilya-2 project % docker compose build --no-cache
[+] Building 15.4s (14/14) FINISHED                                                                                                                                                                                      
 => [internal] load local bake definitions                                                                                                                                                                          0.0s
 => => reading from stdin 584B                                                                                                                                                                                      0.0s
 => [internal] load build definition from Dockerfile                                                                                                                                                                0.0s
 => => transferring dockerfile: 637B                                                                                                                                                                                0.0s
 => [internal] load metadata for docker.io/library/python:3.8.3                                                                                                                                                     1.3s
 => [internal] load .dockerignore                                                                                                                                                                                   0.0s
 => => transferring context: 2B                                                                                                                                                                                     0.0s
 => CACHED [1/7] FROM docker.io/library/python:3.8.3@sha256:dd6cd8191ccbced2a6af5d0ddb51e6057c1444df14e14bcfd5c7b3ef78738050                                                                                        0.0s
 => => resolve docker.io/library/python:3.8.3@sha256:dd6cd8191ccbced2a6af5d0ddb51e6057c1444df14e14bcfd5c7b3ef78738050                                                                                               0.0s
 => [internal] load build context                                                                                                                                                                                   0.0s
 => => transferring context: 293B                                                                                                                                                                                   0.0s
 => [2/7] RUN mkdir /config                                                                                                                                                                                         0.1s
 => [3/7] COPY requirements.txt /config/requirements.txt                                                                                                                                                            0.0s
 => [4/7] RUN python -m pip install --upgrade pip     && pip install --no-cache-dir -r /config/requirements.txt                                                                                                    11.8s
 => [5/7] RUN mkdir /src                                                                                                                                                                                            0.1s 
 => [6/7] WORKDIR /src                                                                                                                                                                                              0.0s 
 => [7/7] ADD . /src                                                                                                                                                                                                0.0s 
 => exporting to image                                                                                                                                                                                              1.7s 
 => => exporting layers                                                                                                                                                                                             1.1s 
 => => exporting manifest sha256:c340a2bc496a1af86b4ef9b3aa0c04303eb53efdd460e9e2a1236e99aa94ac3f                                                                                                                   0.0s 
 => => exporting config sha256:552855ab07583a43b46000950f468dcfe06d530b1a263e0d94f3fe9c3e4ce062                                                                                                                     0.0s
 => => exporting attestation manifest sha256:8fa4c8c9ff95f8c7ad02f86b78303228677652175c212661dd403ed0babfbbdb                                                                                                       0.0s
 => => exporting manifest list sha256:c019dedc989588caf0f86f375d4937397e6e3fb4b2b79afc7f41a36bbab51e0d                                                                                                              0.0s
 => => naming to docker.io/library/project-app:latest                                                                                                                                                               0.0s
 => => unpacking to docker.io/library/project-app:latest                                                                                                                                                            0.6s
 => resolving provenance for metadata file                                                                                                                                                                          0.0s
[+] build 1/1
 ✔ Image project-app Built                                                                                                                                                                                          15.5s

 
dilyam@MacBook-Pro-Dilya-2 project % docker compose up -d
[+] up 6/6
 ✔ Network project_app-network Created                                                                                                                                                                               0.0s
 ✔ Container node              Created                                                                                                                                                                               0.1s
 ✔ Container database          Created                                                                                                                                                                               0.1s
 ✔ Container app               Created                                                                                                                                                                               0.1s
 ✔ Container wordpress         Created                                                                                                                                                                               0.1s
 ✔ Container nginx             Created                                                                                                                                                                               0.0s
```


### Проверила состояние контейнеров
```
dilyam@MacBook-Pro-Dilya-2 project % docker compose ps
NAME        IMAGE                        COMMAND                  SERVICE     CREATED          STATUS          PORTS
app         project-app                  "gunicorn --workers=…"   app         11 seconds ago   Up 10 seconds   
database    mysql:8.0                    "docker-entrypoint.s…"   database    11 seconds ago   Up 10 seconds   3306/tcp, 33060/tcp
nginx       nginx:1.15.12-alpine         "nginx -g 'daemon of…"   nginx       10 seconds ago   Up 10 seconds   0.0.0.0:8081-8083->8081-8083/tcp, [::]:8081-8083->8081-8083/tcp
node        node:16.13.2-alpine3.15      "docker-entrypoint.s…"   node        11 seconds ago   Up 10 seconds   
wordpress   wordpress:5.1.1-fpm-alpine   "docker-entrypoint.s…"   wordpress   10 seconds ago   Up 10 seconds   9000/tcp

```


### Что получилось, скрины в приложенных файлах (/project/screens)
```
| URL | Что внутри | Как Nginx отдаёт запрос |
|-----|------------|-------------------------|
| http://localhost:8081 | Django | `proxy_pass` на gunicorn :8000 |
| http://localhost:8082 | Node.js | `proxy_pass` на node :3000 |
| http://localhost:8083 | WordPress | `fastcgi_pass` на php-fpm :9000 |

```
