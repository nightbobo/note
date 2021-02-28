Docker를 사용하면서 가장 자주 접하는 커맨드는 단연 컨테이너를 실행하기 위해서 쓰이는 docker run일 것입니다. docker run 커맨드는 상당히 여러가지 옵션을 통해 다양한 방식으로 컨테이너를 실행할 수 있도록 해줍니다. 이번 포스트에서는 이중에서 자주 쓰이는 옵션 위주로 dockr run 커맨드를 어떻게 사용하는지 알아보겠습니다.
<br>
<br>

## 기본 포멧

docker run 커맨드의 기본 포멧은 다음과 같습니다. 여기서 이미지 식별자는 필수이며 이미지 ID나 리파지토리(repository):태그(tag)를 사용할 수 있습니다.

```shell
$ docker run (<옵션>) <이미지 식별자> (<명령어>) (<인자>)
```

### -D 옵션 
많은 경우 컨테이너를 백그라운드에서 실행해야 하는데요. 이 때는 -d 옵션을 사용하면 됩니다. -d 옵션을 사용하면 컨테이너가 detached 모드에서 실행되며, 실행 결과로 컨테이너 ID만을 출력합니다. 예를 들어, python:3.8-alpine 이미지로 부터 python -m http.server 명령어를 백그라운드로 실행해보겠습니다. -i 는 -d 와 반대 됨 

```shell 
$ docker run -d python:3.8-alpine python -m http.server
0b920d2f561437418b8fdc0e9bcfdd4c9d634983ded18ba35a4dbae012753a72
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
0e4fe552200f        python:3.8-alpine   "python -m http.serv…"   5 seconds ago       Up 4 seconds                            gifted_chaplygin
```


docker ps 커맨드를 날려보니, 방금 백그라운드로 실행한 컨테이너가 보입니다.<br>
이 명령어를 <Strong>-d </strong>옵션없이 실행했다면, 해당 터미널에서 Ctrl + C를 눌러서 빠져나오는 순간 해당 컨테이너는 종료될 것입니다.

### -it 옵션 
-i 옵션과 -t 옵션은 같이 쓰이는 경우가 매우 많은데요. 이 두 옵션은 컨테이너를 종료하지 않은체로, 터미널의 입력을 계속해서 컨테이너로 전달하기 위해서 사용합니다. 따라서, -it 옵션은 특히 컨테이너의 쉘(shell)이나 CLI 도구를 사용할 때 매우 유용하게 사용됩니다.
```shell 
$ docker run -it python:3.8-alpine
Python 3.8.2 (default, Mar 24 2020, 02:56:01)
[GCC 9.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> print("Hi!")
Hi!
>>>
```

```shell
$ docker run -it python:3.8-alpine /bin/sh
# ls
bin    etc    lib    mnt    proc   run    srv    tmp    var
dev    home   media  opt    root   sbin   sys    usr
# _
```

### -name 옵션 
Docker 컨테이너를 제어할 때 컨테이너 ID를 사용하면 읽거나 기억하기가 어려워서 불편하게 느껴집니다. 이럴 경우, --name 옵션을 사용해서 컨테이너에 이름을 부여해주면 해당 이름으로 컨테이너를 식별할 수 있습니다.

아래 예제에서는 my-server라는 이름으로 컨테이너를 실행한 후에, docker kill 커맨드로 해당 컨테이너를 종료하거나, docker rm 커맨드로 해당 컨테이너를 삭제할 때 컨테이너 이름을 컨테이너 ID 대신에 사용하고 있습니다.

```shell 
$ docker run -d --name my-server python:3.8-alpine python -m http.server
7899108d467cc423e20a3d6cb250070baae01fa541b037707afbbe8d1e9e3000
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
7899108d467c        python:3.8-alpine   "python -m http.serv…"   2 seconds ago       Up 3 second                             my-server
$ docker kill my-server
my-server
$ docker rm my-server
my-server
``` 

### -e 옵션 
Docker 컨테이너의 환경변수를 설정하기 위해서는 -e 옵션을 사용합니다. 또한, -e 옵션을 사용하면 Dockerfile의 ENV 설정도 덮어써지게 됩니다.
아래 커맨드는 FOO 환경 변수를 bar로 세팅을 하고, 환경 변수를 출력하고 있습니다.

```shell 
$ docker run -e FOO=bar python:3.8-alpine env
PATH=/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=92ebed448fb3
FOO=bar
LANG=C.UTF-8
GPG_KEY=E3FF2839C048B25C084DEBE9B26995E310250568
PYTHON_VERSION=3.8.2
PYTHON_PIP_VERSION=20.0.2
PYTHON_GET_PIP_URL=https://github.com/pypa/get-pip/raw/d59197a3c169cef378a22428a3fa99d33e080a5d/get-pip.py
PYTHON_GET_PIP_SHA256=421ac1d44c0cf9730a088e337867d974b91bdce4ea2636099275071878cc189e
HOME=/root
``` 

### -p 옵션
-p 옵션은 호스트와 컨테이너 간의 포트(port) 배포(publish)/바인드(bind)를 위해서 사용되는데요. 호스트(host) 컴퓨터에서 컨테이너에서 리스닝하고 있는 포트로 접속할 수 있도록 설정해줍니다.

아래 커맨드는 컨테이너 내부에서 8080 포트로 리스닝하고 있는 HTTP 서버를 호스트 컴퓨터에서 80 포트로 접속할 수 있도록 해줍니다.

```shell 
$ docker run -d -p 80:8000 python:3.8-alpine python -m http.server
``` 

### -v 옵션 
-w 옵션은 Dockerfile의 WORKDIR 설정을 덮어쓰기 위해서 사용됩니다.

아래 커맨드는 컨테이너의 작업 디렉터리를 /etc로 변경하고 있습니다.

```shell 
$ echo Hi > test.txt
$ docker run -v `pwd`:/etc python:3.8-alpine cat /etc/test.txt
Hi
```

### -w 옵션 
-w 옵션은 Dockerfile의 WORKDIR 설정을 덮어쓰기 위해서 사용됩니다.
아래 커맨드는 컨테이너의 작업 디렉터리를 /etc로 변경하고 있습니다.

```shell
$ docker run -w /etc python:3.8-alpine pwd
/etc
```

### --entrypoint 옵션 
--entrypoint 옵션은 Dockerfile의 ENTRYPOINT 설정을 덮어쓰기 위해서 사용합니다.
예를 들어, python:3.8-alpine 이미지로 부터 python --version을 실행하고 싶다면 다음과 같이 커맨드를 실행하면 됩니다.

```shell
$ docker run --entrypoint python python:3.8-alpine --version
Python 3.8.2
```

### —rm 옵션 
--rm 옵션은 컨테이너를 일회성으로 실행할 때 주로 쓰이는데요. 컨테이너가 종료될 때 컨테이너와 관련된 리소스(파일 시스템, 볼륨)까지 깨끗이 제거해줍니다.

```shell 
$ docker run --rm -it wernight/funbox nyancat
```

