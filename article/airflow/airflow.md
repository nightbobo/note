##### Airflow 
Apache Airflow는 복잡한 계산을 요하는 작업흐름과 데이터 처리 파이프라인을 조율 하기 위해 만든 오픈소스 도구이다. 다양한 인터페이스를 재공하고 있으며 모니터링 프로세스 트러블 슈팅 가시적인 파이프라인을 제공하고 있다.단적인 예로 긴스크립트 실행을 Cron으로 돌리거나 데이터 파이프 파인등 많은 부분에서 훌륭한 기능을 제공하고 있다.

##### Airflow Architecture(기본구조)

- Workers - 할당된 작업 실행 
- Scheduler - 대기열에 필요한 작업 추가 
- Celery - 큐 매커니즘 
- Web server - DAG.task status Infomation 
- Database - 상태관리  Dags , task, variables,etc.
- Redis - Queue 저장소 

[![Airflow Celery Architecture](https://limitlessdatascience.files.wordpress.com/2019/10/image-24.png)](https://limitlessdatascience.files.wordpress.com)

동작방식 Airflow는 Scheduler의 스케줄링으로 Worker에서 작업이 실행되며 동작 방식은 아래와 같다 
- Airflow 에서 Workflow 는 DAG 로 만들어지며 DAG는 Task 들로 구성된다.
- 각 Task는 Operator 클래스를 인스턴스화(멱등성)하여 만든다.[동일 기능을 실행시 동일한 결과를 보장 하고 Task 별 생성된 인턴스에서 실행]
- 스캐줄러 주기에 따른 반복적으로 상태를 확인하며 작업 내용확인시 Celery Excutor를 통해 Message Broker에 작업 내용을 전달한다.  
- Message broker 구성에 따른 Push , Pull 방식으로 worker에 작업이 할당된다. 
- worker 는 할당된 작업을 실행


##### Project Struct 
소스레파지토리 -  [AWS codeCommit](https://google.com, "da-datalake")

주요 파일및 폴더 기능 설명 
- File 
    - DockerFile 이미지 빌드시 참조하는 Resouce File 
    - requirements.txt 컨테이너 구동시 설치가 필요한 package 목록 
    - s21.yml
    - s22.yml 
    - .env 
- Folder 
    - op_script 
    - ccc 


##### Docker 
github에 등록된 [Basis DockerFile](https://github.com/puckel/docker-airflow, "dockerfile") docker를 활용하였습니다. 
  - 변경 항목
    - Apache-airflow 버전 :
    - SQLCLI 버전 변경     : Celery Worker 운영시 문제 발생으로 
  - 추가 항목 
    - Host Os airflow 사용자 추가 : 물리저장소에 ACL 처리시 문제가 발생하여 Host 머신 dummy 사용자및 그룹을 생성
    - docker unix socket 추가    : worker 에서 Host머신에 운영중인 container에게 remoteRestApi 처리시 사용 
    - aws cli 추가               : etl 작업시 다운로드 속도 이슈로 인한 SDK 에서 cli V2 를 사용 하도록 변경 


##### Build 
버전정보를 명시하지 않기에 latest로 등록된다는 점을 운영시 꼭 기억해야 함
da-datalake build docker 
```shell 
$ docker build --rm -t ????/airflow .
```

da-datalake remove container
```shell 
```

da-datalake remove image
```shell
```

###### docker-compose run 
Host에 따른 docker-compose 참조 파일이 나워짐 21번 머신에서 활성화 컨테이너는 worker 뿐이며 22번 머신에서 활성화 컨테이너는 worker, postgre , redis , scheduler 로 구분됨. 참고로 compose를 이용하여 컨테이너 관리시 위존 관계가 있는 컨테이너 끼리 라이프 싸이클을 함께 가져 갈수 있어 운영에 도움이 됨 

```shell 
# 10.99.99.21 머신에서 docker-compose 를 이용한 container 활성화 
$ cd ${airflow:HOME}/
$ docker-compse -f xxxxx up # Start 
# $ docker-compose -f xxxxx stop 
```

```shell 
# 10.99.99.22 머신에서 docker-compose 를 이용한 container 활성화 
$ cd ${airflow:HOME}/
$ docker-compse -f xxxxx up 
# $ docker-compose -f xxxxx stop 
```

##### Usage 
커테이너 내부 접속 
```shell 
$ docker exec -it xxxxxxxx bash 
```
컨테이너 안에 airflow Command invoce 
```shell
$ docker run --rm -ti puckel/docker-airflow airflow list_dags 
```

컨테이너 로그 확인 
```shell 
$ docker logs xxxxx [-f]
```

### DB Persistent File Path 
Dag 및 Task 실행 이력은 DB를 통해 영구적으로 저장 관리 되고 있다. Container(DB)재시작에 따른 파일정보가 누락되면 초기화 되기에 DB File 은 호스트 저장소를 참조하여 운영됨


##### Install python package 
컨테이너 운영시 추가 python package 파일이 필요하면 /requirments.txt 파일을 이용하여 기술해야함. entrypoint.sh 파일 내용을 확인하며 requirements.txt 파일을 참조하여 추가 패키지 설치를 진행함

##### docker Scale 사용불가 
Worker가 실행 하며 생성된 로그는 컨테이너 내에서 관리 되고 있으면 Webserver 에서 워커 실행 로그 확인시 http request 를 통해 로그를 동정으로 노출한다. webserver에서 worker에 http request 호출시 hostName을 사용하는 컨테이너 까지 접속하는 데 이때 scale옵션을 사용하게 되면 container 구동시 장애가 발생하게 된다