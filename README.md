# ДЗ 3: Docker

## Отчет на уровень 4

1) Сделала сборку образа

```
docker build -t counter-app:1 .
```

2) Запустила контейнер

Опубликовала порт 8080 и передала приветствие через переменную окружения `GREETING`:

```
docker run -d --name counter -p 8080:8000 -e GREETING="Привет, Docker" counter-app:1
docker ps
```

Получила это:

```
arina@MacBook-Pro docker-hw % docker ps
CONTAINER ID   IMAGE          COMMAND           CREATED          STATUS          PORTS                                         NAMES
30a304526977   5b64703defe4   "python app.py"   48 minutes ago   Up 48 minutes   0.0.0.0:8080->8000/tcp, [::]:8080->8000/tcp   counter
```

3) Проверила, работает ли счётчик

```
curl http://127.0.0.1:8080/
curl -X POST http://127.0.0.1:8080/increment
curl -X POST http://127.0.0.1:8080/increment
curl -X POST http://127.0.0.1:8080/increment
curl http://127.0.0.1:8080/
```

Получила вот это (немного потыкалась, чтобы четко понимать, на что смотрела, поэтому выводов немного больше):

```
arina@MacBook-Pro docker-hw % curl http://127.0.0.1:8080/
{"greeting": "Привет, Docker", "count": 0, "hostname": "30a304526977", "storage": "sqlite"}%
arina@MacBook-Pro docker-hw % curl -X POST http://127.0.0.1:8080/increment
{"greeting": "Привет, Docker", "count": 1, "hostname": "30a304526977", "storage": "sqlite"}%
arina@MacBook-Pro docker-hw % curl -X POST http://127.0.0.1:8080/increment
{"greeting": "Привет, Docker", "count": 2, "hostname": "30a304526977", "storage": "sqlite"}%
arina@MacBook-Pro docker-hw % curl -X POST http://127.0.0.1:8080/increment
{"greeting": "Привет, Docker", "count": 3, "hostname": "30a304526977", "storage": "sqlite"}%
arina@MacBook-Pro docker-hw % curl http://127.0.0.1:8080/
{"greeting": "Привет, Docker", "count": 3, "hostname": "30a304526977", "storage": "sqlite"}%
arina@MacBook-Pro docker-hw % curl -X POST http://127.0.0.1:8080/increment
{"greeting": "Привет, Docker", "count": 4, "hostname": "30a304526977", "storage": "sqlite"}%
arina@MacBook-Pro docker-hw % curl http://127.0.0.1:8080/
{"greeting": "Привет, Docker", "count": 4, "hostname": "30a304526977", "storage": "sqlite"}%
arina@MacBook-Pro docker-hw % curl http://127.0.0.1:8080/
{"greeting": "Привет, Docker", "count": 4, "hostname": "30a304526977", "storage": "sqlite"}%
```

Увидела, что GET не меняет счетчик, а POST как раз прибавляет 1.

4) Посмотрела логи

```
arina@MacBook-Pro docker-hw % docker logs counter
2026-11-09 21:19:14,954 listening port=8000 storage=sqlite
2026-11-09 21:19:34,942 http 192.168.65.1 "GET / HTTP/1.1" 200 -
2026-11-09 21:19:35,014 http 192.168.65.1 "GET /favicon.ico HTTP/1.1" 404 -
2026-11-09 21:20:29,920 http 192.168.65.1 "GET / HTTP/1.1" 200 -
2026-11-09 21:20:41,701 http 192.168.65.1 "POST /increment HTTP/1.1" 200 -
2026-11-09 21:20:50,464 http 192.168.65.1 "POST /increment HTTP/1.1" 200 -
2026-11-09 21:20:51,947 http 192.168.65.1 "POST /increment HTTP/1.1" 200 -
2026-11-09 21:21:12,705 http 192.168.65.1 "GET / HTTP/1.1" 200 -
2026-11-09 21:22:10,169 http 192.168.65.1 "POST /increment HTTP/1.1" 200 -
2026-11-09 21:22:39,881 http 192.168.65.1 "GET / HTTP/1.1" 200 -
2026-11-09 21:22:42,011 http 192.168.65.1 "GET / HTTP/1.1" 200 -
2026-11-09 21:31:04,827 http 192.168.65.1 "GET / HTTP/1.1" 200 -
2026-11-09 21:31:04,845 http 192.168.65.1 "POST /increment HTTP/1.1" 200 -
2026-11-09 21:31:04,862 http 192.168.65.1 "POST /increment HTTP/1.1" 200 -
2026-11-09 21:31:04,879 http 192.168.65.1 "POST /increment HTTP/1.1" 200 -
2026-11-09 21:31:04,909 http 192.168.65.1 "GET / HTTP/1.1" 200 -
```

В логах видно, что приложение запустилось на порту 8000 и хранит счётчик в sqlite, а дальше записан каждый мой запрос


## Отчет на уровень 6

Инструкция как запустить:

1) Сначала надо скопировать настройки из образца:

```
cp .env.example .env
```

Там можно поменять порт (по умолчанию стоит 8080) и приветствие, у меня "Привет!!!"

2) Как запустить:

```
docker compose up -d --build
```

Что у меня в итоге лежит:
В compose.yaml лежит app, с параметрами порт, приветствие (там стоят метки откуда их брать)
и volume, где будет лежать значение, чтобы не пропало, если (и когда) мы удалим

Мой терминал: 
```
arina@MacBook-Pro docker-hw % docker ps
CONTAINER ID   IMAGE          COMMAND           CREATED          STATUS          PORTS                                         NAMES
30a304526977   5b64703defe4   "python app.py"   48 minutes ago   Up 48 minutes   0.0.0.0:8080->8000/tcp, [::]:8080->8000/tcp   counter
arina@MacBook-Pro docker-hw % cd ~/docker-hw
arina@MacBook-Pro docker-hw % docker stop counter
counter
arina@MacBook-Pro docker-hw % docker rm counter
counter
arina@MacBook-Pro docker-hw % docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
arina@MacBook-Pro docker-hw % cp .env.example .env
arina@MacBook-Pro docker-hw % docker compose config
name: docker-hw
services:
  app:
    environment:
      GREETING: Привет из Compose
    networks:
      default: null
    volumes:
      - type: volume
        source: counter-data
        target: /data
        volume: {}
    build:
      context: /Users/arina/docker-hw
      dockerfile: Dockerfile
    ports:
      - mode: ingress
        host_ip: 127.0.0.1
        target: 8000
        published: "8080"
        protocol: tcp
networks:
  default:
    name: docker-hw_default
volumes:
  counter-data:
    name: docker-hw_counter-data
arina@MacBook-Pro docker-hw % docker compose config
name: docker-hw
services:
  app:
    environment:
      GREETING: Привет из Compose
    networks:
      default: null
    volumes:
      - type: volume
        source: counter-data
        target: /data
        volume: {}
    build:
      context: /Users/arina/docker-hw
      dockerfile: Dockerfile
    ports:
      - mode: ingress
        host_ip: 127.0.0.1
        target: 8000
        published: "8080"
        protocol: tcp
networks:
  default:
    name: docker-hw_default
volumes:
  counter-data:
    name: docker-hw_counter-data
arina@MacBook-Pro docker-hw % cp .env.example .env
arina@MacBook-Pro docker-hw % docker compose config
name: docker-hw
services:
  app:
    environment:
      GREETING: Привет!!!
    networks:
      default: null
    volumes:
      - type: volume
        source: counter-data
        target: /data
        volume: {}
    build:
      context: /Users/arina/docker-hw
      dockerfile: Dockerfile
    ports:
      - mode: ingress
        host_ip: 127.0.0.1
        target: 8000
        published: "8080"
        protocol: tcp
networks:
  default:
    name: docker-hw_default
volumes:
  counter-data:
    name: docker-hw_counter-data
arina@MacBook-Pro docker-hw % docker compose up -d --build
[+] Building 1.8s (13/13) FINISHED                                                          
 => [internal] load local bake definitions                                             0.0s
 => => reading from stdin 488B                                                         0.0s
 => [internal] load build definition from Dockerfile                                   0.0s
 => => transferring dockerfile: 190B                                                   0.0s
 => [internal] load metadata for docker.io/library/python:3.13-slim                    1.5s
 => [auth] library/python:pull token for registry-1.docker.io                          0.0s
 => [internal] load .dockerignore                                                      0.0s
 => => transferring context: 65B                                                       0.0s
 => [1/5] FROM docker.io/library/python:3.13-slim@sha256:e48f59cdba69a3cbd9ed2cf0f348  0.0s
 => => resolve docker.io/library/python:3.13-slim@sha256:e48f59cdba69a3cbd9ed2cf0f348  0.0s
 => [internal] load build context                                                      0.0s
 => => transferring context: 127B                                                      0.0s
 => CACHED [2/5] WORKDIR /app                                                          0.0s
 => CACHED [3/5] COPY app/requirements.txt .                                           0.0s
 => CACHED [4/5] RUN pip install --no-cache-dir -r requirements.txt                    0.0s
 => CACHED [5/5] COPY app/ .                                                           0.0s
 => exporting to image                                                                 0.0s
 => => exporting layers                                                                0.0s
 => => exporting manifest sha256:3d8f3997f408845fb1f7a90be11e6e3c4bd440929b05956d3cf5  0.0s
 => => exporting config sha256:edee74f7e6be1f2d71fe0846ff3047b43174d3ffa6d308281a62e0  0.0s
 => => exporting attestation manifest sha256:ba72230e489126cf7750b6edc50d3b04e12439a8  0.0s
 => => exporting manifest list sha256:51c28397dcfb13baa02df469588f4751de4adef64b69a1e  0.0s
 => => naming to docker.io/library/docker-hw-app:latest                                0.0s
 => => unpacking to docker.io/library/docker-hw-app:latest                             0.0s
 => resolving provenance for metadata file                                             0.0s
[+] up 4/4
 ✔ Image docker-hw-app           Built                                                  3.4s
 ✔ Network docker-hw_default     Created                                                0.0s
 ✔ Volume docker-hw_counter-data Created                                                0.0s
 ✔ Container docker-hw-app-1     Started                                                0.1s
arina@MacBook-Pro docker-hw % docker compose ps
NAME              IMAGE           COMMAND           SERVICE   CREATED          STATUS          PORTS
docker-hw-app-1   docker-hw-app   "python app.py"   app       13 seconds ago   Up 12 seconds   127.0.0.1:8080->8000/tcp
arina@MacBook-Pro docker-hw % curl http://127.0.0.1:8080/
{"greeting": "Привет!!!", "count": 0, "hostname": "763e7eb6c6fc", "storage": "sqlite"}%     arina@MacBook-Pro docker-hw % curl -X POST http://127.0.0.1:8080/increment
{"greeting": "Привет!!!", "count": 1, "hostname": "763e7eb6c6fc", "storage": "sqlite"}%     arina@MacBook-Pro docker-hw % curl -X POST http://127.0.0.1:8080/increment
{"greeting": "Привет!!!", "count": 2, "hostname": "763e7eb6c6fc", "storage": "sqlite"}%     arina@MacBook-Pro docker-hw % curl -X POST http://127.0.0.1:8080/increment
{"greeting": "Привет!!!", "count": 3, "hostname": "763e7eb6c6fc", "storage": "sqlite"}%     arina@MacBook-Pro docker-hw % curl -X POST http://127.0.0.1:8080/increment
{"greeting": "Привет!!!", "count": 4, "hostname": "763e7eb6c6fc", "storage": "sqlite"}%     arina@MacBook-Pro docker-hw % curl -X POST http://127.0.0.1:8080/increment
{"greeting": "Привет!!!", "count": 5, "hostname": "763e7eb6c6fc", "storage": "sqlite"}%     arina@MacBook-Pro docker-hw % curl -X POST http://127.0.0.1:8080/increment
{"greeting": "Привет!!!", "count": 6, "hostname": "763e7eb6c6fc", "storage": "sqlite"}%     arina@MacBook-Pro docker-hw % curl http://127.0.0.1:8080/
{"greeting": "Привет!!!", "count": 6, "hostname": "763e7eb6c6fc", "storage": "sqlite"}%     arina@MacBook-Pro docker-hw % docker compose down
[+] down 2/2
 ✔ Container docker-hw-app-1 Removed                                                    3.2s
 ✔ Network docker-hw_default Removed                                                    0.1s
arina@MacBook-Pro docker-hw % docker compose ps -a
NAME      IMAGE     COMMAND   SERVICE   CREATED   STATUS    PORTS
arina@MacBook-Pro docker-hw % docker volume ls
DRIVER    VOLUME NAME
local     docker-hw_counter-data
arina@MacBook-Pro docker-hw % docker compose up -d
[+] up 2/2
 ✔ Network docker-hw_default Created                                                    0.0s
 ✔ Container docker-hw-app-1 Started                                                    0.1s
arina@MacBook-Pro docker-hw % curl http://127.0.0.1:8080/
{"greeting": "Привет!!!", "count": 6, "hostname": "27776743c059", "storage": "sqlite"}%     
```

Я там опять немного тыкалась, поэтому много строк

Мы Видим, что я досчитала до 6 и удалила контейнер. 
Потом, создав его заново, видно, что он остался но изменился hostname, показывая, что это уже другой человек для нас.