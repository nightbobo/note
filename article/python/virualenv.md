
MAC 환경에서 가상환경 설정 정보 방법을 공유하는 목적으로 작성된 글. 

수향절차 
- 필수 설치 프로그램 
- 가상환경 구성에 따른 선행 프로그램 설치 
- python 버전에 마춰 설치 
- 가상 환경 생성 With 버전 
- 가상 환경 설정 
- 폴더 이동시 자동 실행


필수 설치 package 
    pyenv, pyenv-virtualenv
```shell 
$ brew install pyenv
$ brew install pyenv-virtualenv

.zsh 활성화 
if which pyenv > /dev/null; then eval "$(pyenv init -)"; fi
if which pyenv-virtualenv-init > /dev/null; then eval "$(pyenv virtualenv-init -)"; fi
```


pyenv-virtualenv 사용시 요구되는 프로그램이 존재한다. 
해단 내용은 링크 참조 
https://github.com/pyenv/pyenv/wiki/Common-build-problems


가상환경 구성 
```
# 설치 가능한 인터프린터 목록 확인 
$ pyenv install --list

# 기설치 된 파이썬 버전 
$ pyenv versions
# 특정 인터프린터 설치 
$ pyenv install 3.X.X
```

가상환경 설정 
```shell 
# 프로젝트 폴더로 이동 
$ cd ${project}
# 3.7.4 버전에 sample-env 네임을 사용하는 가상환경울 구성 
$ pyenv virtualenv 3.7.4 ${projectNm}
# 해당 폴더에 local 설정 test-env 를 적용 
$ pyenv activate ${projectNm}
# sample-env 로 설정된 python 버전 확인시 정상작동 되는것으로 봐도 무방 
(${projectNm})$ pyenv versions
# pip list  안내에서 update 하라 말한다. 
(${projectNm})$ python -m pip install --upgrade pip  
(${projectNm})$ pyenv deactivate
$ pyenv virtualenv-delete ${projectNm}

```

