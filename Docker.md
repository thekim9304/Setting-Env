[초보를 위한 도커 안내서](https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html)
[도커 컴포즈를 활용하여 완벽한 개발 환경 구성하기](https://www.44bits.io/ko/post/almost-perfect-development-environment-with-docker-and-docker-compose)

# Container
- 격리된 공간에서 프로세스가 동작하는 기술
- 프로세스를 격리하는 방식
- 도커의 기본 네트워크 모드는 Bridge 모드로 약간의 성능 손실이 있음
  - 네트워크 성능이 주요한 프로그램의 경우 --net=host 옵션을 고려해야함
- 여러개의 컨테이너를 실행하면 서로 영향을 미치지 않고 독립적으로 실행
- 추가되거나 변하는 값은 컨테이너에 저장

# Image
- 컨테이너 실행에 필요한 파일과 설정값등을 포함하고 있는 것
- 상태 값을 가지지 않고 변하지 않음
- 컨테이너가 삭제되더라도 이미지는 변하지 않고 그대로 남아있음

![Docker Image](https://subicura.com/assets/article_images/2017-01-19-docker-guide-for-beginners-1/docker-image.png)

## 레이어 저장방식
![Docker Layer](https://subicura.com/assets/article_images/2017-01-19-docker-guide-for-beginners-1/image-layer.png)

- 도커 이미지는 컨테이너를 실행하기 위한 모든 정보를 갖고 있기 때문에 보통 용량이 수백 메가임
  - 그런데 기존 이미지에 파일 하나 추가했따고 수백메가를 다시 다운받는다면 매우 비효율적임
- 도커는 이러한 문제를 해결하기 위해 layer라는 개념을 사용
- Image는 여러개의 읽기 전용(read only) layer로 구성되고 파일이 추가되거나 수정되면 새로운 layer가 생성
##

- 컨테이너를 생성할 때도 layer 방식을 사용함
- 기존의 이미지 layer 위에 읽기/쓰기(read/write) layer를 추가
  - 이미지 layer를 그대로 사용하면서 컨테이너가 실행중에 생성하는 파일이나 변경된 내용은 읽기/쓰기 레이어에 저장됨
  - 그래서 여러개의 컨테이너를 생성해도 최소한의 용량만 사용함

## 이미지 경로
![](https://subicura.com/assets/article_images/2017-01-19-docker-guide-for-beginners-1/image-url.png)
- 이미지는 url 방식으로 관리하며 태그를 붙일 수 있음
- 이런 방식은 이해하기 쉽고 편리하며 태그 기능을 잘 이용하면 테스트나 롤백도 쉽게 할 수 있음

## Dockerfile
- 도커는 이미지를 만들기 위해 Dockerfile 이라는 파일에 자체 DSL(Domain-specific Language)언어를 이용해 이미지 생성 과정을 적음
- 프로그램과 여러 의존성 패키지에 대한 정보를 Dockerfile로 관리

# 실습
## 연결
### 포트 연결
- 도커 컨테이너의 특정 포트를 가상머신에 연결하고 다시 mac의 포트와 연결

### 디렉토리 연결
- 디렉토리를 가상머신과 공유하고 그 디렉토리를 다시 컨테이너와 연결
###

- mac에서 제공하는 xhyve라는 가상환경 이용

## 설치 확인
입력
> docker version

출력
> Client: Docker Engine - Community
 Cloud integration: 1.0.7
 Version:           20.10.2
 API version:       1.41
 Go version:        go1.13.15
 Git commit:        2291f61
 Built:             Mon Dec 28 16:12:42 2020
 OS/Arch:           darwin/amd64
 Context:           default
 Experimental:      true
>
> Server: Docker Engine - Community
 Engine:
  Version:          20.10.2
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.13.15
  Git commit:       8891c58
  Built:            Mon Dec 28 16:15:28 2020
  OS/Arch:          linux/amd64

- Client와 Server 정보가 정상적으로 출력됐으면 설치 완료
- 도커 커맨드를 입력하면 도커 클라이언트가 도커 서버로 명령을 전송하고 결과를 받아 터미널에 출력
![](https://subicura.com/assets/article_images/2017-01-19-docker-guide-for-beginners-2/docker-host.png)

## 컨테이너 실행
#### 도커 실행 명령어 (run)
> docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]

###### [OPTIONS]
- -d : detached mode, 백그라운드 모드
- -p : 호스트와 컨테이너의 포트를 연결 (포워딩)
- -v : 호스트와 컨테이너와 디렉토리를 연결 (마운트)
- -e : 컨테이너 내에서 사용할 환경변수 설정
- -name : 컨테이너 이름 설정
- -rm : 프로세스 종료시 컨테이너 자동 제거
- -it : -i와 -t를 동시에 사용한 것으로 터미널 입력을 위한 옵션
- -link : 컨테이너 연결
- -w : 컨테이너 안의 프로세스가 실행될 디렉터리를 설정
- --volume : 로컬 디렉터리의 특정 경로를 컨테이너 내부로 마운트

> docker run ubuntu:16.04

> Unable to find image 'ubuntu:16.04' locally
16.04: Pulling from library/ubuntu
4007a89234b4: Pull complete
5dfa26c6b9c9: Pull complete
0ba7bf18aa40: Pull complete
4c6ec688ebe3: Pull complete
Digest: sha256:e74994b7a9ec8e2129cfc6a871f3236940006ed31091de355578492ed140a39c
Status: Downloaded newer image for ubuntu:16.04

1. run 명령어를 사용하면 사용할 이미지가 저장되어 있는지 확인
2. 없다면 다운로드(pull)
3. 컨테이너 생성(create)
4. 시작(start)
##

- 위 경우에 컨테이너는 정상적으로 실행됐지만 뭘 하라고 명령어를 전달하지 않았기 때문에 컨테이너는 생성되자마자 종료됨
- 컨테이너는 프로세스이기 때문에 실행중인 프로세스가 없으면 컨테이너는 종료됨

#### 컨테이너 목록 확인 (ps)
> docker ps

#### 컨테이너 중지하기 (stop)
> docker stop [OPTIONS] CONTAINER [CONTAINER...]

- 실행중인 컨테이너를 하나 또는 여러개(띄어쓰기 구분) 중지할 수 있음
- ID의 앞 부분만 입력해도됨 (겹치지 않을만큼)

> docker ps # get containser ID
> docker stop [CONTAINER ID]
> docker ps -a # show all containers

#### 컨테이너 제거하기 (rm)
> docker rm [OPTIONS] CONTAINER [CONTAINER...]
> docker ps -a # check exist

- 컨테이너를 삭제한다는건 컨테이너에 생성된 파일이 사라진다는 뜻
###

- 중지된 컨테니어 한 번에 삭제하기
> docker rm -v $(docker ps -a -q -f status=exited)

#### 이미지 목록 확인하기 (images)
> docker images [OPTIONS] [REPOSITORY[:TAG]]

- 이미지가 너무 많이 쌓이면 용량을 차지하므로 사용하지 않는 이미지는 지우는게 좋음

#### 이미지 다운로드 (pull)
> docker pull [OPTIONS] NAME[:TAG|@DIGEST]

- 같은 태그지만 이미지가 업데이트 된 경우에 pull 명령어를 통해 새로 다운로드

#### 이미지 삭제하기(rmi)
> docker rmi [OPTIONS] IMAGE [IMAGE...]

- images를 통해 확인한 이미지 ID를 입력해 삭제
- 컨테이너가 실행중인 이미지는 삭제되지 않음

#### 컨테이너 로그 보기 (logs)
> docker logs [OPTIONS] CONTAINER

> docker ps
> docker logs [CONTAINER ID]

- --tail : 마지막 n 줄만 출력
> docker logs --tail 10 [CONTAINER ID]

- -f : 실시간 로그 확인
  - ctrl + c로 중지
> docker logs -f [CONTAINER ID]

#### 컨테이너 명령어 실행하기 (exec)
> docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
- 실행중인 컨테이너에 들어가보거나 컨테이너의 파일을 실행할 때 사용
##

- run과 유사해보이는데 run은 컨테이너 실행이고 exec는 실행중인 컨테이너에 명령어를 내리는 것

## 컨테이너 업데이트
- 컨테이너를 새로운 버전으로 업데이트
  1. 새 버전의 이미지를 다운(pull)
  2. 기존 컨테이너 삭제(stop, rm)
  3. 새 이미지를 기반으로 새 컨테이너 실행(run)
- 컨테이너 삭제 => 컨테이너에 생성된 파일이 사라짐
  - **그래서 데이터는 반드시 컨테이너 내부가 아닌 외부 스토리지에 저장해야함**
    - 데이터 볼륨(Data volumes)을 컨테이너에 추가
    - 클라우스 서비스 사용
- 데이터 볼륨을 사용하면 해당 디렉토리는 컨테이너와 별도로 저장되고 컨테이너를 삭제해도 데이터가 지워지지 않음

### 데이터 볼륨을 사용하는 방법
#### 1. 호스트의 디렉토리를 마운트
- run 명령어의 옵션인 -v 사용
- Ex
  - before
    > docker run -d -p 3306:3306 \\
  -e MYSQL_ALLOW_EMPTY_PASSWORD=true \\
  --name mysql \\
  mysql:5.7

  - after
    > docker run -d -p 3306:3306 \\
    -e MYSQL_ALLOW_EMPTY_PASSWORD=true \\
    --name mysql \\
    -v /my/own/datadir:/var/lib/mysql \\ # <- volume mount
    mysql:5.7

- 위 예시는 "/my/own/datadir/" 디렉토리를 컨테이너의 "/var/lib/mysql/" 디렉토리로 마운트한것

## Docker Compose
- 지금까지의 과정은 간단하므로 터미널에서 명령어로 작업할 수 있었지만 실제론 컨테이너 조합도 많아지고 여러가지 설정이 추가되며 명령어가 복잡해짐
- 도커는 복잡한 설정을 쉽게 관리하기 위해 YAML방식의 설정파일을 이용한 Docker Compose라는 툴을 제공함
- Docker for Mac을 설치했으면 자동으로 설치됨
##

- Docker compose를 사용하면 컨테이너 실행에 필요한 옵션을 docker-compose.yml이라는 파일에 적어둘 수 있고, 컨테이너 간 실행 순서나 의존성도 관리할 수 있음

#### docker-compose.yml 작성
- 한 docker-compose.yml 안에 있는 서비스들ㅇ느 별도로 지정하지 않으면 하나의 네트워크에 속함
```YAML
version: "3" # Docker compose 중에서 3으로 시작하는 최신 버전 사용

services:
  db: # 서비스 이름
    image: postgres # db 서비스에서 사용할 도커 이미지
    volumes: # 디렉터리 마운트
      - ./data:/home/user/data # [로컬 경로]:[컨테이너 경로]
    environment: # 컨테이너 환경변수 설정
      - POSTGRES_DB=sampledb
      #...
  django:
    build: # 작성한 Dockerfile 빌드
      context: .
      dockerfile: ./Dockerfile
    ports: # 포트 연결 ([호스트 포트]:[컨테이너 포트]
      - "8000:8000"
    command: python test.py
      # 셋팅 후 마지막에 수행할 명령어
      # jupyter notebook 등
    volumes:
      - ./:/app/
```

- Dockerfile 예시
```Dockerfile
FROM python:3

RUN apt-get update && apt-get -y install libpq-dev

WORKDIR /app
COPY ./requirements.txt /app/
RUN pip install -r requirements.txt # -r : 주어진 요구사항 파일(requirements.txt) 설치

# CMD ["python", "test.py"]
# docker-compose.yml의 command 부분 (command가 관리하기 더 쉬움)
```

### 도커 컴포즈 주요 명령어
#### up [options]
- docker-compose.yml 파일의 내용에 따라 이미지를 빌드하고 실행
> docker-compose up -d

  1. 서비스를 띄울 네트워크 설정
  2. 필요한 볼륨 생성(혹은 이미 존재하는 볼륨과 연결)
  3. 필요한 이미지 풀(pull)
  4. 필요한 이미지 빌드(build)
  5. 서비스 의존성에 따라 서비스 실행

- 옵션
  - -d : 서비스 실행 후 콘솔로 빠져나옴 (docker run에서의 -d와 같음)
  - --force-recreate : 컨테이너를 지우고 새로 만듦
  - --build : 서비스 시작 전 이미지를 새로 만듦

#### ps
- 현재 환경에서 실행 중인 각 서비스의 상태를 보여줌
> docker-compose ps

#### stop [SERVICE...], start [SERVICE...]
- 서비스를 멈추거나, 멈춰 있는 서비스를 시작
> docker-compose stop

> docker-compose start

#### down [options]
- 서비스를 지움
> docker-compose down --volume

  - --volume : 볼륨까지 삭제함

#### logs [options] [SERVICE...]
- 서비스의 로그를 확인할 수 있음
- 서비스 이름 안주면 도커 컴포즈가 관리하는 모든 서비스의 로그를 함께 보여줌
> docker-compose logs service1

  - -f : 실시간 로그 출력 (계속해서)

### docker-compose.yml 파일을 수정했다면?
- 원래는
  1. 서비스를 멈추고(stop)
  2. 서비스를 지우고(rm)
  3. 서비스를 시작(up)

- up 명령만 실행해도 위 과정 알아서 해줌
> docker-compose up -d service1

### Dockerfile 파일을 수정했다면?
- Dockerfile을 수정했을 땐 build 명령을 사용해 도커 이미지를 새로 만들어야함
  - 이후 서비스 stop, rm, up

- 하지만 up 명령에 --build 옵션 추가하면 알아서 이미지 새로 만들고 서비스 재시작
> docker-compose up -d --build service1


---
## 도커 이미지 만들기 (Dockerizing)
- 도커는 이미지를 만들기 위해 컨테이너의 상태를 그대로 이미지로 저장하는 방법을 사용함
![](https://subicura.com/assets/article_images/2017-02-10-docker-guide-for-beginners-create-image-and-deploy/create-image.png)
- 어떤 애플리케이션을 이미지로 만든다면 리눅스만 설치된 컨테이너에 애플리케이션을 설치하고 그 상태를 그대로 이미지로 저장
##

#### Dockerfile (빌드 파일)
- 도커는 이미지를 만들기 위해 **Dockerfile**이라는 이미지 빌드용 DLS(Domain Specific Language) 파일을 사용한다.
- 쉘 스크립트로 작성
- 초보자는 실제 OS(ubuntu)에서 명령어를 실행해보고 작성
- Dockerfile(서비스용)과 Dockerfile-dev(개발용) 분리해서 사용 (하지만 업데이트는 동일하게)
  - 우분투에서 실행
  ``` shell
  # 1. ubuntu 설치 (패키지 업데이트)
  apt-get update

  # 2. ruby 설치
  apt-get install ruby
  gem install bundler

  # 3. 소스 복사
  mkdir -p /usr/src/app
  scp Gemfile app.rb root@ubuntu:/usr/src/app  # From host

  # 4. Gem 패키지 설치
  bundle install

  # 5. Sinatra 서버 실행
  bundle exec ruby app.rb
  ```

- Dockerfile
  - 하나의 Dockerfile은 기본적으로 다음과 같은 구조를 가진 여러 개의 명령문으로 구성
  > [명령어(Instruction)] [인자(arguments)]

  - 위 shell script를 dockerfile로 작성
  ```Dockerfile
  FROM ubuntu:18.04
  RUN apt-get install -y update

  RUN apt-get install -y sudo

  COPY . /usr/src/test

  WORKDIR /usr/src/test

  # Anaconda installing
  RUN wget https://repo.anaconda.com/archive/Anaconda3-2020.02-Linux-x86_64.sh
  RUN bash Anaconda3-2020.02-Linux-x86_64.sh -b
  RUN rm Anaconda3-2020.02-Linux-x86_64.sh

  # Set path to conda
  ENV PATH /usr/src/test/anaconda3/bin:$PATH

  # Install the desired package
  RUN pip install numpy
  ```
    - FROM    : base 이미지 지정
      - FROM [image]:[tag] (FROM ubuntu:16.04)
    - RUN     : 명령어 실행
      - RUN [command]
    - COPY    : 파일 복사
      - COPY [src] [dest] (COPY ./ /usr/src/test)
    - WORKDIR : 컨테이너 상에서 작업 디렉토리를 전환(=cd)
      - WORKDIR /path/to/workdir
    - ENV     : 환경 변수 설정

    - VOLUME  : 컨테이너 외부에 파일시스템 마운트
      - volume [

#### Docker build
> docker build [OPTIONS] PATH | URL |

- -t      :  생성할 이미지 이름

```shell
docker build -t app ./
```

- 빌드 명령어를 실행하면 Dockerfile에 정의한 내용이 한 줄 한 줄 실행된다.

## Docker Image Refactoring
#### Base Image
- 왠만한 애플리케이션은 이미 이미지로 만들어져있음
- 세부적인 설정 필요한거 아니면 그냥 가져다 쓰자

#### Build Cache
- 한 번 빌드한 이미지를 다시 빌드하면 굉장히 빠르게 완료됨
  - 처음 이미지를 빌드하는 과정에서 각 단계를 이미지 레이어로 저장하고 다음 빌드에서 캐시로 사용하기 때문
###

- Dockerfile의 명령어가 수정됐거나 추가하는 파일이 변경되면 cache가 깨지고 새로운 이미지를 만듦
  - 변견 지점부터
- 그러므로 수정될 부분들은 후순위로

#### 명령어 최적화
- 이미지를 빌드할 때 불필요한 로그는 무시
```shell
RUN apt-get -y -qq update
```
  - -qq : 로그 출력 안함

##

- 마지막으로 명령어는 비슷한 것끼리 묶기

##
# 이미지 저장소
## 이미지 태그

> [Registry URL]/[사용자 ID]/[이미지명]:[tag]

  - Registry URL : 기본적으로 Docker hub를 가리킴
  - 사용자 ID : Default = "library"
    > ubuntu == library/ubuntu == docker.io/library/ubuntu

#### 도커의 tag 명령어
- 기존에 만든 이미지에 추가로 이름을 지어줄 수 있음

> docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]

```shell
docker tag app testid/test-app:1
```
  - app 이미지에 계정 정보와 버전 정보 추가
  - testid라는 ID를 사용하고 이미지 이름을 test-app으로 변경
  - 태그는 1 (버전 정보라 생각하자)

#### docker push
```shell
docker push testid/test-app:1
```

## [도커를 이용한 웹서비스 무중단 배포하기](https://subicura.com/2016/06/07/zero-downtime-docker-deployment.html)
