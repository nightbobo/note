익숙치 않은 파이썬 환경에서 개발을 진행해야 하는 상황이다.
파이썬을 쓰면 쓸수록 매력적인 언어 인듯 하다. 
메인 언어인 자바보다 많이 유연하고 확장성이 좋아보인다. 
유연성과 확장성에 있어 좋긴하지만 아쉬운 부분이 많이 보인다. 
대표적으로 하위 버전에 따른 하위 호환성 문제이다. 
자바를 예를 들면 1.4 , 1.5 , 8 ... 버전이 올라간다 해도 
과거에 개발된 소스가 돌아가지 않는 경우는 흔치 않다. 
반대로 파이썬은 하위 호환성 보다 편의성 및 퍼포먼스에 초점을 마춘듯 하다. 
이로 인해 버전에 따른 문제가 언제나 초보인 나에게 문제로 보인다. 
호환성 관련 많은 관리 기술이 존재하는듯 하고 표준으로 정해진 스팩이 존재하지 않아. 
사용하는 사람에 따라 환경 구성을달리 하는듯 하다.<br> 

구글링을 하면 많은 아티클을 접하게 되는데 그중 관심 pyenv , autoenv,가용성에 있어 최고 인듯 하나다. 
데이터 분식시 유용하게 사용될수 있는 anaconda 역시 pyenv 를 통해 버전닝을 관리할수 있으니 현제 내 기준으로는 최고인듯 하다.

- pyenv 
  - 파이썬 버전을 관리하는 툴 
  - 하나의 컴퓨터에 다양한 파이썬 버전을 설치하고 관리 
  - 프로젝트 홈페이지 :  https://github.com/pyenv/pyenv

- pipenv 
  - 파이썬 가상환경 버추얼 환경 구성 
  - 패키지 매니징 

- autoenv 
  - autoenv 는 디렉토리 이동시 실행되는 툴 
  - pyenv-virtualenv 사용 시 불편한 수작업을 자동화.
  - 프로젝트 폴더로 들어가면 .env파일 실행하여 가상환경 활성화.

- pip 
  - 파이썬 라이브러리 관리 



---


## Pyenv 
Pyenv는 여러 파이썬 버전을 설치하고, 쉽게 선택해서 사용할 수 있도록 해준다. 
- 사용자 컴퓨터 환경의 글로벌 파이썬 버전을 교체하고 관리할 수 있다. 
- 프로젝트 단위로 파이썬 버전을 다르게 설정할 수 있다. 
- 환경변수(PYENV_VERSION)을 통해 파이썬 버전을 지정할 수 있다. 
- 다양한 파이썬 버전을 쉽게 검색할수 있고 , Tox 를 통해 다양한 파이썬 버전에서 테스트 할수 있도록 도와준다. 

MacOS에서 pyenv설치는 brew install pyenv가 가장 편리하고 안전하다. 
그밖에 소스 코드를 직접 cline 받아 설치하는 방법과 자동 설치 도구 pyenv-installer 도 있다. 

```shell
# 파이썬 설치 
# Step1. xcode 설치 
$ xcode-select --install 
...
# Step2. python3 설치 
$ brew install python3
...
# Step3. pyenv 설치
$ brew install pyenv
$ pyenv --version 
pyenv 1.2.23
```

기본적인 설치는 완료되었다. 
이제 pyenv 를 이용하여 프로젝트 새팅을 진행해 보자. <br>
첫번째로 pyenv 를 이용하여 설치 가능 버전을 확인 해보자. 

### pyenv install --list 
설치 가능한 버전 정보 확인
```shell 
$ pyenv install --list 
# 아나콘다및 미니콘다 까지 설치 가능하다. 
```

### pyenv install 3.8.7
파이썬을 3.8.0을 머신에 설치 활성화(X)<br>
pyenv 를 통해 설치한 파이썬버전이 확인 된다. 
<br>
중요한 사실 하나는 global 버전이 바뀌지 않는다. 
설치 이후 python3 --version 을 실행하면 
3.9.x 버전이 노출된다. 

```shell
# python 3.8.7 설치 
$ pyenv install 3.8.7
# pyenv를 들어가 설치된 버전 확인 
$ cd $HOME/.pyenv/versions
$ ls -al 
total 0
drwxr-xr-x  3 sanghyun  staff   96  2 28 22:22 .
drwxr-xr-x  4 sanghyun  staff  128  2 28 21:46 ..
drwxr-xr-x  6 sanghyun  staff  192  2 28 22:24 3.8.7
```

### pipenv를 이용한 프로젝트 python 버전 설정 
프로젝트 루트 패스 에 접속하여 pipenv 를 이용하여 python 버전을 설정 한다. 
```shell
$ pyenv install 3.8.7
$ mkdir -p  template_project
$ cd  template_project
# 이미 설치된 버전 
$ pyenv versions 
$ pipenv --python 3.8.7
Creating a virtualenv for this project..
... 
... 
✔ Successfully created virtual environment!
Virtualenv location: /Users/sanghyun/.local/share/virtualenvs/template_project-h1xYF3pB
Creating a Pipfile for this project...

# virtualenv 생성에 따른  설정파일 'Pipfile' 확인 
$ ls -al
```
별도에 설치(python) 과정 없이 해당 프로젝트에 가상환경이 생성된다. 
차후에 Pipfile 에 생성된 내용을 한번더 기술할 생각이다. 지금은 가볍게 가상환경 구성에 따른 설정파일이 생긴다고 생각하고 넘어가자. 

### pipenv 가상환경 활성화 
Pipfile 을 이용하여 가상환경 활성화 
```shell 
$ pipenv shell
```

### pipenv 를 이용한 패키지 설치 
pipenv를 이용하면 Pipfile로 requirements.txt를 대체할수 있으며, Pipfile.lock 파일을 생성해서 개발환경과 프로덕션 환경에 동일한 패키지가 설치될 수 있도록 보장한다. 
패키지 설치이후 Pipfile.lock 파일 생성 여부만 확인하자 
```shell
(template_project)
$ pipenv install flask==1.1.1
(template_project)
$  which python
/Users/sanghyun/.local/share/virtualenvs/template_project-h1xYF3pB/bin/python 
```
여기서 중요한게 하나 있는데 어디에 해당 패키지가 설치 되는 냐다.<br>
인터프리터 패스에서 site-packages 에 설치된 패키지 'flask' 가 확인 된다. 
~/.local/share/virtualenvs/template_project-h1xYF3pB/lib/python3.8/site-packages

이렇게 pipenv를 이용하여 python 버전 관리 및 프로젝트 구성시 사용되는 패키지 관리 항목에 대한 전반적인 내용을 살표 보았다. 
위에서 기술한 적이 있는 PipFile , Pipfile.lock 파일 관련 쫌더 깊게 알아보자. 

---
위에서 pipenv를 이용하여 패키지 관리 항목을 확인 했다.이번 아티클은 pipenv를 이용하면 취할수 있는 장점을 간단히 설명하고 pipenv 이용시 중요한 Pipfile , Pipfile.lock 파일에 대해 알아보자. 

## Why? 
전통적으로 requirements.txt을 이용하여 패키지 관리를 진행 했다고 한다(시작하는 단계에서는 공감이 1도 안됨ㅎㅎ) 
문제는 인스톨과 매니지 먼트 가 불리되어 있어 패키지 갱신이후 사용자가 requirements.txt 를 갱신해줘야 하는 치명적인 담점이 있다. 
또한 Dev 환경에서는 requirements-dev.txt 을 만들고 이또한 수동으로 변경해줘야 번거로움에 연속인 것이다. 

pipenv는 이런 불편함을 해소하여 파이썬 기반 프로젝트의 dependencies를 관리를 더 간단하게 할 수 있도록 해준다.

## Getting Started 
해당 내용은 패스한다 위 기술한 프로젝트 새팅 항목을 참조 내용과 중복된다. 
새로 등장하는 commend 만 집고 가보자
pipenv lock 커맨드 실행시 lock 파일이 갱신된다. Pipfile.lock 파일은 실재 운영중인 패키지 상세(버전) 정보 까지 갱신되는걸로 보인다. 

```shell 
# 설치된 패키지 이름과 버전이 Pipfile.lock 파일에 업데이트 된다. 
$ cd  template_project 
$ pipenv shell
(template_project)
$ pipenv lock
... 
✔ Success!
Updated Pipfile.lock (477275)!
```

## 개발환경 관리 
운영 환경과 별개로 개발환경에서만 필요한 패키지 정보가 존재한다. 일예로 Test 모듈이 대표적이다. 
'--dev' 를 이용하여 설치시 앞에서 기술한 Pipfile 내용중 dev 내용에 추가된 모듈이 확인 될것이다.  
```shell
(template_project)
$ pipenv install --dev pytest

```

## 프로젝트 초기설정 
초기 프로젝트 설정 시  Pipfile 을 이용하여 가상환경을 설정할수 있다.
e.g. 소스에서 운로드후 Pipfile 을 이용하여 패키지 다운로드시 


```shell
$  pipenv sunc 
```




---
## autoenv 








#### 프로젝트 위치 찾기
$ pipenv --where
#### virtualenv 위치 찾기
$ pipenv --venv
#### python 인터프리터 위치 찾기
$ pipenv --py
