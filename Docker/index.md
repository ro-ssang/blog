## 도커 초기화 명령

### 시스템 상의 모든 컨테이너 삭제

```sh
docker container rm -f $(docker container ls -aq)
```

> 💡 `docker container rm -f <CONTAINER ID>` 는 실행중인 컨테이너도 바로 삭제할 수 있다.

### 사용하지 않고 있는 컨테이너 삭제

```sh
docker container prune
```

### 사용하지 않고 있는 이미지 삭제

```sh
# dangling 상태의 이미지 삭제
docker image prune

# dangling 상태의 이미지와 사용하지 않고 있는 이미지 삭제
docker image prune -a
```

> 💡 dangling이란?
>
> 이미지 목록에서 태그가 없어 `<none>`으로 보이는 이미지



```sh
# 실행중인 컨테이너 목록 출력
docker container ls

# 대상 컨테이너에서 실행중인 모든 프로세스 정보 출력
docker container top <CONTAINER ID>

# 대상 컨테이너에서 수집된 모든 로그 출력
docker container logs <CONTAINER ID>

# 대상 컨테이너의 상세 정보 출력
docker container inspect <CONTAINER ID>

# 대상 컨테이너의 상태 확인 (CPU, 메모리, 네트워크, 디스크 사용량 확인)
docker container stats <CONTAINER ID>
```



```sh
# 이미지 내려받기
docker image pull <IMAGE NAME or IMAGE ID>

# 이미지 빌드
docker image -t <TAG NAME> <DOCKER FILE PATH>

# 이미지 히스토리 확인하기
docker image history <IMAGE NAME>

# 이미지 목록 출력
docker image ls

# 사용중인 디스크 용량
docker system df
```

> 💡 이미지 레이어 캐시를 이용한 Dockerfile 스크립트 최적화
>
> Dockerfile 스크립트의 인스트럭션은 각각 하나의 이미지 레이어와 1:1로 연결된다. 그러나 인스트럭션의 결과가 이전 빌드와 같다면, 이전에 캐시된 레이어를 재사용한다. 만약 인스트럭션이 이전 빌드와 다르다면 해당 단계의 인스트럭션부터 다시 실행되기 때문에 **Dockerfile 스크립트의 인스트럭션은 잘 수정하지 않는 인스트럭션이 앞으로 오고 자주 수정되는 인스트럭션이 뒤에 오도록 배치돼야 한다**.

```dockerfile
FROM diamol/node

CMD ["node", "/web-ping/app.js"]

ENV TARGET="blog.sixeyed.com" \
    METHOD="HEAD" \
    INTERVAL="3000"

WORKDIR /web-ping
COPY app.js .
```



## 컨테이너의 생성과 삭제, 실행, 정지

### 컨테이너 생성 및 실행

`docker run` 명령어는 도커 컨테이너를 **생성** ( `docker create` )하고 **실행** ( `docker run` )하는 기능을 한다. 만약 컨테이너 생성에 필요한 이미지가 필요하다면 **이미지를 내려받는 기능** ( `docker image pull` )도 겸한다.

```sh
docker run --name <container name> -d <image name>
```

| 옵션                    | 내용                   |
| ----------------------- | ---------------------- |
| --name <container name> | 컨테이너 이름을 지정함 |
| -d                      | 백그라운드로 실행함    |

### 컨테이너 정지

```sh
docker stop <container name>
```

### 컨테이너 삭제

⚠️ 컨테이너를 삭제하려면 먼저 반드시 컨테이너를 정지시켜야 한다.

```sh
docker rm <container name>
```

### 컨테이너 목록 출력

`-a` 옵션을 추가하면 현재 실행중인 컨테이너 목록 + 정지 상태의 컨테이너 목록을 출력한다.

```sh
docker ps -a
```



## 컨테이너의 통신

컨테이너 외부에서 컨테이너 내부로 접근하려면, 컨테이너 외부에서 컨테이너 내부로 접속하기 위한 **포트 설정**이 필요하다.

```sh
# 포트를 지정하는 옵션
-p <host port number>:<container port number>
```

```sh
docker run --name <container name> -d -p <host port number>:<container port number> <image name>
```

| 옵션                    | 내용                                             |
| ----------------------- | ------------------------------------------------ |
| --name <container name> | 컨테이너 이름을 지정함                           |
| -d                      | 백그라운드로 실행함                              |
| -p 8080:80              | 호스트의 포트 8080을 컨테이너 포트 80으로 포워딩 |



## 이미지 삭제

컨테이너를 삭제해도 이미지는 그대로 남아 있는데, 이미지가 늘어나면 스토리지 용량을 압박하게 되므로 필요없어진 이미지는 그때그때 삭제하도록 한다.

### 이미지 삭제

⚠️ 이미지를 삭제하려면 해당 이미지로 실행된 컨테이너를 모두 삭제해야 한다.

```sh
docker image rm <image name>
```

### 이미지 목록 출력

```sh
docker image ls
```



## 네트워크


### 네트워크 생성

```sh
docker network create <network name>
```

###  네트워크 삭제

```sh
docker network rm <network name>
```

###  네트워크 목록 출력

```sh
docker network ls
```



## 컨테이너와 호스트 간에 파일 복사하기

### 컨테이너 > 호스트 복사

```sh
docker cp <호스트 경로> <컨테이너 이름>:<컨테이너 경로>
```

### 호스트 > 컨테이너 복사

```sh
docker cp <컨테이너 이름>:<컨테이너 경로> <호스트 경로>
```



## 스토리지 마운트

### 볼륨 생성

```sh
docker volume create <volume name>
```

### 볼륨 삭제

```sh
docker volume rm <volume name>
```

### 볼륨 목록 출력

```sh
docker volume ls
```

### 볼륨 마운트

```sh
# 볼륨 마운트를 지정하는 옵션
-v <볼륨 이름>:<컨테이너 마운트 경로>
-volume <볼륨 이름>:<컨테이너 마운트 경로>

# 실행 명령어
docker run --name <container name> -d -p <host port number>:<container port number> -v <volume name>:<container mount path> <image name>
```

### 바인드 마운트

```sh
# 바인드 마운트를 지정하는 옵션 1
-v <스토리지 실제 경로>:<컨테이너 마운트 경로>

# 실행 명령어
docker run --name <container name> -d -p <host port number>:<container port number> -v <storage path>:<container mount path> <image name>

# 바인드 마운트를 지정하는 옵션 2
--mount type=bind,source=<스토리지 실제 경로>,target=<컨테이너 마운트 경로>

# 실행 명령어
docker run --name <container name> -d -p <host port number>:<container port number> --mount --mount type=bind,source=<storage path>,target=<container mount path> <image name>
```



## 컨테이너로 이미지 만들기

### 컨테이너를 이미지로 변환

```sh
docker commit <컨테이너 이름> <새로운 이미지 이름>
```

### Dockerfile 스크립트로 이미지 만들기

```dockerfile
FROM httpd
COPY ./index.html /usr/local/apache2/htdocs/
```

```sh
docker build -t <생성할 이미지 이름> <재료 폴더 경로>
```



## 도커 컴포즈

### 도커 컴포즈란?

시스템 구축과 관련된 명령어를 하나의 텍스트 파일에 기재해 명령어 한번에 시스템 전체를 실행하고 종료와 폐기까지 한번에 하도록 도와주는 도구

```yaml
version: "3"
services:
  mysql000ex11:
    image: mysql
    networks:
      - wordpress000net1
    volumes:
      - mysql000vol11:/var/lib/mysql
    restart: always
    command: mysqld --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci --default-authentication-plugin=mysql_native_password
    environment:
      MYSQL_ROOT_PASSWORD: myrootpass
      MYSQL_DATABASE: wordpress000db
      MYSQL_USER: wordpress000kun
      MYSQL_PASSWORD: kunpass

  wordpress000ex12:
    depends_on:
      - mysql000ex11
    image: wordpress
    networks:
      - wordpress000net1
    volumes:
      - wordpress000vol12:/var/www/html
    ports:
      - 8081:80
    restart: always
    environment:
      WORDPRESS_DB_HOST: mysql000ex11
      WORDPRESS_DB_USER: wordpress000kun
      WORDPRESS_DB_PASSWORD: kunpass
      WORDPRESS_DB_NAME: wordpress000db

networks:
  wordpress000net1:

volumes:
  mysql000vol11:
  wordpress000vol12:
```



## 멀티 스테이지

```dockerfile
FROM diamol/base AS build-stage
RUN echo "Building..." > /build.txt

FROM diamol/base AS test-stage
COPY --from=build-stage /build.txt /build.txt
RUN echo "Testing..." >> /build.txt

FROM diamol/base
COPY --from=test-stage /build.txt /build.txt
CMD ["cat", "/build.txt"]
```

