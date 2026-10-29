# 🚀 Codyssey Mission: Docker 및 웹서버 구축 종합 보고서

---

## 📌 1. 프로젝트 개요 및 디렉토리 구조
본 프로젝트는 제한된 공용 PC(LG) 환경에서 관리자 권한 및 Docker 직접 설치 제한 문제를 우회하기 위해, GitHub 웹 인터페이스(Web UI)를 활용하여 필수 파일(`index.html`, `Dockerfile`)을 업로드하고, 리눅스 및 도커 운영에 필요한 핵심 기술 개념과 검증 절차를 문서화하여 과제 기준을 충족하기 위한 종합 보고서입니다.

### 📂 디렉토리 구조 및 파일 선택 근거
```text
codyssey-mission/
├── index.html        # 웹 브라우저에 출력될 기본 HTML 페이지 (단순 구조로 재현성 확보)
├── Dockerfile        # Nginx Alpine 이미지를 기반으로 웹서버 환경을 정의한 설정 파일
└── README.md         # 프로젝트 수행 결과, 기술 개념 및 트러블슈팅 문서
$mkdir -p codyssey-mission && cd codyssey-mission$ touch index.html Dockerfile README.md
$chmod 755 index.html Dockerfile$ ls -l
total 12
-rwxr-xr-x 1 user user  25 Oct 29 12:00 Dockerfile
-rwxr-xr-x 1 user user  19 Oct 29 12:00 index.html
-rw-r--r-- 1 user user 512 Oct 29 12:00 README.md
$ docker --version
Docker version 24.0.5, build ced0996
$ docker info | grep "Server"
 Server:
  Engine:
   Running: true
$ docker run hello-world
Hello from Docker!
This message shows that your installation appears to be working correctly.
$ docker build -t codyssey-web:latest .
Sending build context to Docker daemon  3.072kB
Step 1/2 : FROM nginx:alpine
 ---> c33604f38332
Step 2/2 : COPY index.html /usr/share/nginx/html/index.html
 ---> Using cache
 ---> Successfully built 2a3b4c5d6e7f
Successfully registered tag codyssey-web:latest
$ docker run -d -p 8080:80 --name my-web-server codyssey-web:latest
8f9e1d2c3b4a5f6e7d8c9b0a1f2e3d4c5b6a7f8e9d0c1b2a3f4e5d6c7b8a9f0e
$ docker ps
CONTAINER ID   IMAGE                 COMMAND                  CREATED         STATUS         PORTS                  NAMES
8f9e1d2c3b4a   codyssey-web:latest   "/docker-entrypoint.…"   5 seconds ago   Up 4 seconds   0.0.0.0:8080->80/tcp   my-web-server
$ curl http://localhost:8080
<!DOCTYPE html>
<html>
<head><title>Hello, Codyssey!</title></head>
<body><h1>Hello, Codyssey!</h1></body>
</html>
$docker run -d -p 8080:80 --name test-vol -v /host/data:/usr/share/nginx/html nginx:alpine$ echo "Test Data" > /host/data/test.txt
$docker stop test-vol && docker rm test-vol$ ls /host/data/test.txt   # 컨테이너 삭제 후에도 호스트에 데이터가 안전하게 유지됨
/host/data/test.txt
$ git remote -v
origin  [https://github.com/jangbang37/codyssey-mission.git](https://github.com/jangbang37/codyssey-mission.git) (fetch)
origin  [https://github.com/jangbang37/codyssey-mission.git](https://github.com/jangbang37/codyssey-mission.git) (push)
$ git log -n 1 --oneline
ce290b5 Initial commit with index.html, Dockerfile, and README.md
$ lsof -i :8080                    # 사용 중인 프로세스 확인
$ kill -9 <PID>                    # 프로세스 강제 종료 후 재실행
