도커를 사용하면서 알아두면 유용한 내용이 있어 이를 기술해 놓는다. 


### 운영중인 컨테이너에 bash 로 접속 
운영중인 컨테이너 안에 접속 
```shell 
$ docker exec -it web /bin/bash
$ docker exec -it airflow-docker_webserver_1 bash
```

### 실행중인 컨테이 중지 
운영 중인 컨테이너 중지 시키기 
```shell 
$ docker stop $(docker ps -aq)
```

### 중지된 컨테이너 재실행 
중지된 컨테이너를 시작하기
```shell 
$ docker ps -a
CONTAINER ID   IMAGE                          COMMAND                 ... NAMES
4d51e50ad3ca   puckel/docker-airflow:1.10.9   "/entrypoint.sh webs…"  ... docker-airflow_webserver_1
097a230939f6   postgres:9.6                   "docker-entrypoint.s…"  ... docker-airflow_postgres_1

$ docker start docker-airflow_webserver_1
``` 

airflow test my_python_dag task_1 2021-02-03









이거하나 찾아보자. 
$ HOSTIP=`ip -4 addr show scope global dev eth0 | grep inet | awk '{print $2}' | cut -d / -f 1 | sed -n 1p`
$ docker run  --add-host=docker:${HOSTIP} --rm -it debian
'cut' , 'sed', 'awk' 

