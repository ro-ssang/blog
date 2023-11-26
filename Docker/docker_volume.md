## Volume이란?

볼륨은 도커 컨테이너에서 생성되거나 사용된 데이터를 유지시키기 위해 선호되는 메커니즘

## data 유지 방법

1. **volume**
   
    container의 데이터를 host machine의 `/var/lib/docker/volumes` 에 docker가 자동 생성한 hash를 사용하여 저장
    
    ```bash
    # CLI
    $ docker run -it -v <컨테이너 volume 디렉토리> <이미지>
    
    # 예시 (컨테이너의 /home/node/app의 데이터를 호스트 머신의 /var/lib/docker/volumes/#hash에 저장)
    $ docker run -it -v /home/node/app node:lts
    ```
    
    ```yaml
    # docker compose file
    services:
      frontend:
        image: node:lts
        volumes:
          - /home/node/app
    ```
    
2. **bind mount**
   
    container의 데이터를 host machine의 특정 디렉토리(or 파일)와 맵핑
    
    ```bash
    # CLI
    $ docker run -it -v <호스트 머신 디렉토리>:<컨테이너 volume 디렉토리> <이미지>
    
    # 예시 (컨테이너의 /home/node/app의 데이터를 아래 명령어를 실행중인 호스트 머신의 디렉토리에 저장)
    $ docker run -it -v "$(pwd)":/home/node/app node:lts
    ```
    
    ```yaml
    # docker compose file
    services:
      frontend:
        image: node:lts
        volumes:
          - .:/home/node/app
    ```
    
3. **tmpfs**
   
    host machine의 메모리에 저장
    

## References

* [Docker Document - Volumes](https://docs.docker.com/storage/volumes/)
* [Docker Document - Bind mounts](https://docs.docker.com/storage/bind-mounts/)
* [docker 데이터 활용 - bind mount(1)](https://nerd-mix.tistory.com/47)
* [도커(Docker) Volume 사용법](https://0902.tistory.com/6)