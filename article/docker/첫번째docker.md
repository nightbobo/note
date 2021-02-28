# Docker 컨테이너, 이미지 

도커 사용이 전문한 나에게 운영 환경이 Docker 기반으로 운영될 예정이라 
급하게 레퍼런스 하게 되었다. 
생각 보다 많은 내용이 담겨 있어 이에 생각을 정리할겸 정리하는 레퍼런스한 내용을 기준으로 글을 남길 예정이다. <br>
딱 쓸수 있는 만큼 .. 거기 까지가 나에 한게일듯 <br>
어디 까지나 이글은 지식 전파에 목적보다는 나름 학습 결과를 저장하는 수준이니 상세 내용은 .... <br>
상세한 내용을 원하신다면 홈페이지를 방분하여 확인 하시는것도 하나에 방법일듯 합니다. 

## 컨테이너 이미지 차이 
간단히 내가 이해한 부분을 정리하면 컨테이너는 경리 환경을 제공하는 프로세스의 실행 환경이라 생각한다. <br>
최초 프로세스 실행시 이미지 정보를 참조하여 경리 환경을 구성한다. <br>
여기서 중요한 한가지가 있는데 컨테이너가 이미지 정보는 실행시에만 참조된다는 것이다.<br>
(정확히는 이는 틀린 예기이다. 운영시 발생하는 변경분은 이미지 내에서 계속 변경 이력이 싸이고 있는 느낌이다(??))<br>
예를 들어 장시간 컨테이너가 운영시 내부 운영 resource 가 변경되면 그것은 최초 생성된 이미지와 어면히 다른 상태를 보인다. <br>


### 이미지 변경 이력 확인 실습
- docker pull {image}<br>
Docker Repository 에 등록된 image를 다운로드 한다. 

```shell
# Docker 이미지 다운로드 
$ docker pull ubuntu:bionic                                                                                                                     sanghyun@parksanghyunui-MacBookPro
bionic: Pulling from library/ubuntu
d519e2592276: Pull complete
d22d2dfcfa9c: Pull complete
b3afe92c540b: Pull complete
Digest: sha256:ea188fdc5be9b25ca048f1e882b33f1bc763fb976a8a4fea446b38ed0efcbeba
Status: Downloaded newer image for ubuntu:bionic
docker.io/library/ubuntu:bionic
$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
ubuntu       bionic    c090eaba6b94   5 weeks ago   63.3MB
```
- docker run -it ubuntu:bionic bash <br>
이미지 기반 컨테이너에 접속 (접속보다 좋은 단어가 생각나지 않아 접속이라 적었다. 경리된 환경에 bash에 붙은 것 뿐이다.  )

```shell
$ docker run -it ubuntu:bionic bash
[root@a1e52f3aaff4 /]# apt update
... 
[root@a1e52f3aaff4 /]# apt install -y git
... 
[root@a1e52f3aaff4 /]# git --version
git version 2.17.1
```

- docker ps <br>
프로세스 목록 조회 
```shell
$ docker ps 
CONTAINER ID   IMAGE           COMMAND   CREATED          STATUS          PORTS     NAMES
ac1df35dca45   ubuntu:bionic   "bash"    27 seconds ago   Up 26 seconds             bold_shamir
```

- 컨테이너생성시 참조한 이미지와 운영시 컨테이너 환경에 차이가 발생 
```shell 
$  docker diff ac1df35dca45 | head                                                                                                               sanghyun@parksanghyunui-MacBookPro
C /bin
A /bin/less
A /bin/lesskey
A /bin/lessecho
A /bin/lessfile
A /bin/lesspipe
C /usr
C /usr/lib
C /usr/lib/x86_64-linux-gnu
A /usr/lib/x86_64-linux-gnu/libedit.so.2
```

<strong>이존 이미지 정보와 컨테이너에서 실행중이 내용에 차이가 발생 했다.</strong>
- 컨영 내용으로 image 만들기  
```shell 
root@ac1df35dca45:/# exit

$ docker commit  ac1df35dca45 ubuntu:lasted_with_git

$ docker images                                                                                                                                 REPOSITORY   TAG               IMAGE ID       CREATED         SIZE
ubuntu       lasted_with_git   14f327fede43   4 seconds ago   193MB
ubuntu       bionic            c090eaba6b94   5 weeks ago     63.3MB
```

<strong>위 코드 영역에서 확인 하는 것처럼 이둘 이미지는 서로 다르다</strong><br>


### 2. docker Image 만들기 
도커 이미지는 프로비저닝 환경에서 빛을 바란다. 
요즘 같이 설치 해야 하는 항목이 많아 질수록 개발자에 부담은 증가한다. 
컨테이너에서 사용한 이미지를 목적에 마춰 생성할수 있으면 컨테이너(경리된 환경)에서 부리없이 실행가능한 온전한 가상머신이 생성된다고 보면 된다. 

여기서 생각할w 내용이 하나 있는데 프로비저닝 시에만 빛을 바라는 것인가? 하는 의문점이다. 
이내용은 뒤에서 다시 다려 보려 한다. 

#### DockerFile 
아래 예시 파일 기준으로 DockerFile 설정 항목을 다뤄보려한다. 
대부분에 지시어에 따른 구문은 주석을 참조하면 된다. 
여기서 또 중요한 한가지가 나왔다. 'RUN' 과  'CMD bash -c ' 항목이다. 

내가 이해한 명료한 기준은 어느 단계에서 실행 되는가 이다. <br>
'RUN'은 이미지 생성시 최초 한번 실행 된다고 보면 된다.<br> 
'CMD bash -c'는 컨테이너 구동시 항상 실행 된다고 보면 된다. 

```
# Base Image 
FROM ubuntu:14.04

# 이미지 생성시 실행. Part1
RUN apt-get update &&\
  apt-get -qq -y install git curl build-essential apache2 php5 libapache2-mod-php5 rcs

# 컨테이너 구동시 안에서 실행 파일 
WORKDIR /tmp

# 이미지 생성시 실행. Part2
RUN \
  curl -L -O https://github.com/wkpark/moniwiki/archive/v1.2.5p1.tar.gz &&\
  tar xf /tmp/v1.2.5p1.tar.gz &&\
  mv moniwiki-1.2.5p1 /var/www/html/moniwiki &&\
  chown -R www-data:www-data /var/www/html/moniwiki &&\
  chmod 777 /var/www/html/moniwiki/data/ /var/www/html/moniwiki/ &&\
  chmod +x /var/www/html/moniwiki/secure.sh &&\
  /var/www/html/moniwiki/secure.sh

# 이미지 생성시 실행. Part3 
RUN a2enmod rewrite

# 가상 머신 enviroment 
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2

# Open Port 
EXPOSE 80

# 이미지 생성이 아닌 컨테이너 실행시 실행 
CMD bash -c "source /etc/apache2/envvars && /usr/sbin/apache2 -D FOREGROUND"
```

- Dockerfile 을 이용한 이미지 생성 
  
```
# -t 옵션은  TagName     마지막 '.' 은 Path 정보이다. 더 정확히는 위에 작성한 Dockerfile을 참조한 Path 
$ docker build -t nacyot/moniwiki:latest . 
```

- 생성된 이미지를 참조한 컨테이너 실행 
```
# -d는 백그라운드에서 실행하는 Option 
# -p는 포트 푸워딩 Option 9999로 들어오면 컨테이너(가상 환경) 80 포트로 포워딩 된다고 보면 된다. How to는 모른다.  
$ docker run -d -p 9999:80 nacyot/moniwiki:latest
```

이미지 생성은 어떤 의미가 있고 이미지 생성은 어떻게 하는지에 대해 살펴 보았다. 

먼가 실행하면서 한가지 이상한 점을 발견했다. 
아래 기술한 커맨드를 실행시 컨테이너 프로세스 항목이 복수로 표기된다는 것이다.

```
Session1. $ docker run -it ubuntu:bionic bash
Session2. $ docker run -it ubuntu:bionic bash
Session3. $ docker ps
CONTAINER ID   IMAGE           COMMAND   CREATED          STATUS          PORTS     NAMES
0e2c29f0f4ea   ubuntu:bionic   "bash"    12 seconds ago   Up 11 seconds             eager_maxwell
b0fbf9e9587e   ubuntu:bionic   "bash"    20 seconds ago   Up 19 seconds             infallible_mirzakhani
```
하나에 이미지를 가지고 실행되는 컨테이너 프로세스 확인시 복수에 컨테이너가 확인된다. <br>
<strong>하나에 이미지를 공유해도 위 두 컨테이너는 다른 상태를 가진다. </strong><br>
1번째 내용을 강조하고 싶어 억지스러운 상황을 한번 만들어 봤다. <br>











## 컨테이너 라이프 싸이클 
운영을 해야 하는 시점에 간지러운 부분을 글어준다. 
https://racoonlotty.tistory.com/entry/Docker-컨테이너-라이프-사이클?category=730029
