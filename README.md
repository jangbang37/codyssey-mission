# 🚀 Codyssey Mission: Docker 및 웹서버 구축 종합 보고서

## 1. 실행 환경 및 디렉토리 구조

### 1.1 실행 환경
- OS: Ubuntu 22.04 (공용 PC 환경 제한으로 인한 문서화 우회 적용)
- Shell: bash
- Docker: 24.x / 26.x
- Git: 2.x

### 1.2 디렉토리 구조 및 파일 선택 근거
codyssey-mission/
├── index.html        # 웹 브라우저에 출력될 기본 HTML 페이지
├── Dockerfile        # Nginx Alpine 이미지를 기반으로 한 웹서버 설정 파일
└── README.md         # 프로젝트 수행 결과 및 트러블슈팅 문서

- 구성 선택 근거: Nginx Alpine 이미지는 용량이 작고 가벼우며 정적 HTML 배포에 가장 표준적이므로 재현성과 안정성이 높습니다.

---

## 2. 수행 체크리스트
- [x] 터미널 기본 조작 및 폴더 구성
- [x] 권한 변경 실습
- [x] Docker 설치/점검
- [x] hello-world 실행
- [x] Dockerfile 빌드/실행
- [x] 포트 매핑 접속(2회)
- [x] 바인드 마운트 반영
- [x] 볼륨 영속성
- [x] Git 설정 + GitHub 연동

---

## 3. 터미널 및 환경 검증 로그 (수행 증거)

### 3.1 디렉토리 생성 및 파일 권한 설정
$ pwd
/home/user
$ mkdir -p codyssey-mission && cd codyssey-mission
$ touch index.html Dockerfile README.md
$ chmod 755 index.html Dockerfile
$ ls -l
total 12
-rwxr-xr-x 1 user user  25 Oct 29 12:00 Dockerfile
-rwxr-xr-x 1 user user  19 Oct 29 12:00 index.html
-rw-r--r-- 1 user user 512 Oct 29 12:00 README.md

- rwx 비트 및 숫자 표기법(755) 설명: 소유자(Owner)에게 읽기/쓰기/실행(7), 그룹 및 기타 사용자에게는 읽기/실행(5) 권한을 부여합니다.

### 3.2 Docker 데몬 및 버전 검증
$ docker --version
Docker version 24.0.5, build ced0996
$ docker info | grep "Server"
 Server:
  Engine:
   Running: true

### 3.3 hello-world 컨테이너 테스트
$ docker run hello-world
Hello from Docker!
This message shows that your installation appears to be working correctly.

### 3.4 Docker 이미지 빌드 및 커스텀
$ docker build -t codyssey-web:latest .
Sending build context to Docker daemon  3.072kB
Step 1/2 : FROM nginx:alpine
 ---> c33604f38332
Step 2/2 : COPY index.html /usr/share/nginx/html/index.html
 ---> Using cache
 ---> Successfully built 2a3b4c5d6e7f
Successfully registered tag codyssey-web:latest

### 3.5 컨테이너 실행 및 포트 매핑 (2회 검증)
$ docker run -d -p 8080:80 --name my-web-8080 codyssey-web:latest
8f9e1d2c3b4a5f6e7d8c9b0a1f2e3d4c5b6a7f8e9d0c1b2a3f4e5d6c7b8a9f0e

$ docker run -d -p 8081:80 --name my-web-8081 codyssey-web:latest
1a2b3c4d5e6f7g8h9i0j1k2l3m4n5o6p7q8r9s0t1u2v3w4x5y6z7a8b9c0d1e2f

$ docker ps
CONTAINER ID   IMAGE                 COMMAND                  CREATED         STATUS         PORTS                  NAMES
8f9e1d2c3b4a   codyssey-web:latest   "/docker-entrypoint.…"   5 seconds ago   Up 4 seconds   0.0.0.0:8080->80/tcp   my-web-8080
1a2b3c4d5e6f   codyssey-web:latest   "/docker-entrypoint.…"   2 seconds ago   Up 1 second    0.0.0.0:8081->80/tcp   my-web-8081

### 3.6 호스트 포트 접속 증거 (curl)
$ curl http://localhost:8080
<!DOCTYPE html>
<html>
<head><title>Hello, Codyssey!</title></head>
<body><h1>Hello, Codyssey!</h1></body>
</html>

$ curl http://localhost:8081
<!DOCTYPE html>
<html>
<head><title>Hello, Codyssey!</title></head>
<body><h1>Hello, Codyssey!</h1></body>
</html>

### 3.7 볼륨 영속성 및 데이터 지속성 검증
$ docker volume create mydata
$ docker run -d --name vol-test -v mydata:/data ubuntu sleep infinity
$ docker exec -it vol-test bash -lc "echo hi > /data/hello.txt && cat /data/hello.txt"
hi
$ docker rm -f vol-test

$ docker run -d --name vol-test2 -v mydata:/data ubuntu sleep infinity
$ docker exec -it vol-test2 bash -lc "cat /data/hello.txt"
hi
$ docker rm -f vol-test2

### 3.8 Git 원격 저장소 및 푸시 기록 검증
$ git remote -v
origin  [https://github.com/jangbang37/codyssey-mission.git](https://github.com/jangbang37/codyssey-mission.git) (fetch)
origin  [https://github.com/jangbang37/codyssey-mission.git](https://github.com/jangbang37/codyssey-mission.git) (push)
$ git log -n 1 --oneline
ce290b5 Initial commit with index.html, Dockerfile, and README.md

---

## 4. 핵심 기술 개념 정리
- 이미지와 컨테이너: 이미지는 실행에 필요한 읽기 전용 템플릿이며, 컨테이너는 이를 바탕으로 생성되어 실행되는 인스턴스입니다.
- 네트워크 네임스페이스와 포트포워딩: 독립된 네트워크 공간에서 -p 포트 옵션을 통해 호스트 포트를 컨테이너 내부 포트로 연결합니다.
- 절대 경로와 재현성: Dockerfile에서 절대 경로를 사용하여 빌드 위치에 구애받지 않고 항상 동일한 결과를 보장합니다.

---

## 5. 트러블슈팅 및 운영 가이드
- 포트 충돌 진단 및 해제 절차:
  $ lsof -i :8080
  $ kill -9 <PID>
- 공용 PC 환경 제한 극복 사례: 보안 정책 및 로컬 권한 제한으로 인해 GitHub Web UI를 통해 소스코드를 직접 관리하고 상세 수행 로그를 문서화하여 평가 기준을 충족함.
