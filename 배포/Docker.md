# Docker?

 Gitpod 사이트 사용

feture periview가 설정되있어야 docker 명령어 사용 가능



sudo docker-up 명령어 입력

doker 입력  : 명령어 종류 확인

cat  /etc/os-release  : 현재사용중인 os 확인

docker run -it centos bash  : docker에서 실행중일때 백그라운드나 실시간으로 소통 실행

cat /etc/os-release  

exit :

## Docker 이미지 생성

1. gitpod 계정

2. gitpod setting  feture peiview code 클릭 

3. gitpod 인스턴스 생성 
   본인의 git레포지토리 이동 
   레포지토리 주소앞에 gitpod.io/# 붙임
   pip install -r requirements.txt    

4. whitenoise 설치

   1. pip install whitenoise
   2. middleware 에서 securitymmidddleware 바로아래
      'whitnoise.middleware.WhitNoiseMiddleware' 추가

5. Gunicorn 설치
   pip install gunicon

6. Dorkerfile 생성

   ```
    From python:3.8
    ENV PYTHONUNBFFERED=1
    RUN mkdir /app
    WORKDIR /app
    COPY . /app  :현재위치를 복사
    RUN apt-get undate W
      && apt-get install -y W
      python3 python3-pip python3-dev python3-venv build-essential libpq-dev w
      && rm -rf /var/lib/apt/lists/*
   RUN pip install -r requirements.txt
   RUN chmod +x /app/tun.sh
   EXPOSE 8000
   
   ENTRYPOINT ["/app/run.sh"]
   1.run.sh파일 생성
   	#!/bin/bash
   	python manage.py migrate
   	python manage.py collectstatic
   	gunucorn lionproject.wsgi -b 0.0.0.0:8000
   ```

   ```
   1.pip freeze > requirements.txt : #requirements 파일 생성
   2.sudo docker-up : 이창을 닫으시면 안댑니다
   3.Ctrl +shift + ` : 키로 새로운 터미널 열기
   4.docker build -t (DockerHubld)도커허브아이디ㅌ /django-app :도커 이미지 생성
   5.docker run -it -p 8000:8000 DockerHubld/django-app :생성된 도커 이미지 실행
   6.docker login : Docker Hub에 로그인(이미지 업로드용)
   7.docker push DockerHubld/django-app : Docker Hub에 생성된 이미지 업로드
   8.https://hub.docker.com/r/DockerHubld/django-app에서 내앱이 업로드 된것 
   ```

   

# Docker 서버배포하기

1. 준비사항

   1. EC2 인스턴스(AWS EC2 전반부 참고)\
   2. Docker Hub에 등록된 이미지

2. 서버 세팅

   1. Docker 설치

      curl -fsSL https://get .docker .com | bash

   2. Docker Compose 설치

      1. sudo curl -L "https://github.com/docker/compose/releases/download/1.28.5/docker

         -compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose :docker-compse 바이너리 다운로드

      2. sudo chmod +x /usr/local/bin/docker-compose : docker-compose 바이너리에 실행 권한 부여

   3. 앱 설치 폴더 지정

      1. sudo mkdir /opt/django-app
      2. cd /opt/django-app

   4. docker-compose.yml 생성

      1. vi docker-compose.yml 

insert로 창변경

```
version: "3.9"

services:
 db:
 	image: postgress
 	environment:
 	 - POSTGRES_DB=${DB_NAME}
 	 - POSTGRES_USER=${DB_USER}
 	 - POSTGRES_PASSWORD=${DB_PASSWORD}
 web:
 	image: DockerHubld/django-app
 	environment:
 	  - DB_NAME=${DB_NAME}
 	  - DB_USER=${DB_USER}
 	  - DB_PASSWORD=${DB_PASSWORD}
 	  - DB_HOST=db
 	  - DEBUG=${DEBUG}
 	ports:
 	  - "8000:8000"
 	depends_on:
 	  - db
```

- 저 내용을 그대로 터미널에 복사할경우 띄어쓰기 문제가 발생
- pastebin.com 등의 서비스에 업로드후  wget 사용추천
- sudo wget https://pastbin.com/raw/pastekd -0 docker -compose.yml



1. .env파일 생성

```
DB_NAME=원하는거
DB_USER=
DB_PASSWORD=
DEBUG=False
```

1. DB 최초 실행(db 생성, 사용자 등록 등 진행)
   sudo docker -compose up db

   ctrl + c

2. 잘 작동 되는지 테스트

   1. sudo docker-compose up
   2. 내ip:8000 접속 되는지 확인

3. 백그라운드 모드로 전환

   sudo docker-compose up -d

:wq누르면 타출



- ## Docker 이미지 자동 생성하기(feat.github)

1. 준비사항

   1. Docker로 배포하기가 끝난 레포
   2. github계정

2. Github의 내 레포로 이동하기

3. Actions 버튼 선택

   set up a~ 버튼 클릭

   이름을 docker-publish.yml설정 후 아래 내용 작성

```
name: Docker Publish

on:
 publish:
 	branches:[ main ]
 	
 #Allows you to run this workflow manually from the Actions tab
 workflow_dispatch

jobs:
 build:
  runs-on: ubuntu-latest
  
  steps:
   - uses:  actions/checkout@v2
   
   -name: Docker Build
    run: docker build -t ${{ secrets.DOCKER_USERNAME }}/my-app
    
   -name:Docker Push
    run:
    	docker login -u ${{ secrets.DOCKER_USERNAME }} -p ${{ secrets.DOCKER_PASSWORD}}
    	docker push ${{ secrets.DOCKER_USERNAME }}/my-app
```

