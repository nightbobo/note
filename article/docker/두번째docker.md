# docker-compose 

앞에서 Docker 관련 컨테이너 이미지에 대해서 알아 봤다.<br>
실 운영 환경에서는 이미지 생성시 복수에 Process 가 요구 되는 경우가 대부분이다.<br> 
프로비저닝에서 엄청난 장점을 가진다는건 Docker를 더장쓰기 의한 방법으로 compose를 알아보자<br>
다시 한번 강조하지만 이건 기술전파에 목적이 아닌 나에 주관가 현시점 생각을 정리하기 위한 내용이 담겨 있다. 
잘못된 부분은 전달해주시면 고맙겠고 질책은 삼가해 주세요.<br>

지금 후회 하고 있다 가장 좋은 REF : 홈페이지다. https://docs.docker.com/compose/

### 개발 환경 구성시 문제 
Python 특징인지 몰라도 하위 호환성을 전혀 고려 하지 않는다. 그로인해 필요 패키지 설치시 의존성에 큰 문제가 발생하는 경우가 다반사이다. 

### compose 특징 
설치할 부품들이 늘어날수록 고통도 늘어납니다. 이걸 쉽게 도와준다.<br>
예를 들어 하나에 서비스를 구성하는 격리된 환경을 만든다고 생각해보자. 
하나의 서비스를 하기위해선 필요한 어플리케이션이 다수 존재한다. 쫌더 직관적으로 백엔드, 프론트엔드 , DB ... 등 
Dockerfile 을 이용하여 가상환경 구성시 다수에 어플리케이션 설정은 가능하나 불편한이 더 크게 느껴진다. 
compose는 이렇게 하나에 이미지에서 다수에 어플리케이션을 포함한 이미지 생성에 많은 도움을 준다. 

### 안으로 docker-compose.yml
> 다양한 예제 파일을 기준보면서 해당 기능에 대해 확인한다. 

yarm 파일을 이용하여 해당 compose 설정 정보를 관리한다.
docker compose  airflow  설정 파일을 보는게 직관 적일듯 
docker-compose-LocalExecutor.yml
```yml
version: '2.1' 
        # docker-compose 버전입니다. 작성일 기준으로 3.0을 권장하지만 아직 2.1을 쓰고 있네요.  
        # 서비스는 `postgres`와 `webserver` 두개로 구성되어 있습니다. 

services:
    postgres:
        image: postgres:9.6                 # db는 postgres 공식 이미지를 가져옵니다.
        environment:                        # db 구성에 필요한 값들을 넣어줍니다.
            - POSTGRES_USER=airflow
            - POSTGRES_PASSWORD=airflow
            - POSTGRES_DB=airflow

    webserver:                              # 여기서 부터 airflow 네요.
        image: puckel/docker-airflow:1.10.3 # puckel 계정 아래 있는 이미지 사용
        restart: always                     # 컨테이너 중단된 경우, 자동으로 재시작 해주는 옵션
        depends_on:
            - postgres                      # postgres를 db로 사용하니까 의존성 설정을 해줍니다.
        environment:
            - LOAD_EX=n                     # 여기는 이미지를 구성할때 설정한 환경변수를 넣어줍니다.
            - EXECUTOR=Local                # 아래에서 이미지 구성 파일을 보며 확인하겠습니다.
        volumes:
            - ./dags:/usr/local/airflow/dags # 현재 경로의 dags 폴더를 컨테이너 dags에 마운트합니다.
            # Uncomment to include custom plugins
            # - ./plugins:/usr/local/airflow/plugins
        ports:
            - "8080:8080"                   # 컨테이너 port 8080을 localhost의 8080으로 맞춰줍니다.
        command: webserver                  # 다 끝나면 웹서버 명령어로 ui를 띄우네요.
        healthcheck:                        # -f 로 webserver pid가 잘 생성이 되었는지 확인합니다.
            test: ["CMD-SHELL", "[ -f /usr/local/airflow/airflow-webserver.pid ]"]
            interval: 30s  # 30초 마다 하네요.
            timeout: 30s   # 30초 동안 기다려 주고
            retries: 3     # 3번 재시도.. 합니다. 자주 죽어서 쓰나봅니다.
```

yml 파일 에서 가장 중요한 항목은 services 하위 Element 들이다 . 
services 하위 Element는 하나에 애플리케이션? 이라고 보자 
(나름 정리를 위해 어플리케이션이라고 씀 이는 정확한 표현이 아닙니다.)
```yml
    postgres:
        image: postgres:9.6                 # 이미지 정보 Dockerfile 을 생각 해보면 From 에 해당 할듯 
```


다른 예에서 build : .  항목이 존재하는데 
이는 docker-compose 를 이용해 컨테이너 생성시 Dockerfile 을 참조하여 이미지를 생성한다. 
```yml
webserver:
    build: .     #  이부분 Dockerfile을 이용하여 이미지 생성 
    restart: always
    depends_on:
      - postgres
    volumes:
      - ./airflow_files/dags:/usr/local/airflow/dags
    ports:
      - "8080:8080"
    entrypoint: airflow webserver
    healthcheck:
      test: ["CMD-SHELL", "[ -f /usr/local/airflow/airflow-webserver.pid ]"]
      interval: 30s
      timeout: 30s
      retries: 3
```

```yml
```




 


