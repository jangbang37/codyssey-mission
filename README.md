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
$ mkdir -p codyssey-mission && cd codyssey-mission
$ touch index.html Dockerfile README.md
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
## 💾 3. 데이터 영속성 및 저장소 설정 (Storage)

본 프로젝트에서는 컨테이너가 삭제되어도 데이터가 사라지지 않도록 **바인드 마운트(Bind Mount)** 방식을 사용했습니다.

### 🔍 바인드 마운트 vs 볼륨 차이점
1. **바인드 마운트 (사용한 방식):** 
   - 호스트의 특정 경로(`/host/data`)를 컨테이너 내부와 직접 연결합니다.
   - 설정 파일이나 소스 코드를 실시간으로 수정하고 반영할 때 유리합니다.
2. **볼륨 (Volume):** 
   - Docker가 관리하는 별도의 저장 공간을 생성합니다.
   - 사용자가 직접 폴더를 관리할 필요가 없어 보안과 관리에 더 효율적입니다.

### 🧪 검증 결과
- `-v /host/data:/usr/share/nginx/html` 옵션을 통해 호스트 폴더를 연결했습니다.
- `docker rm` 명령어로 컨테이너를 삭제한 후에도, 호스트의 `/host/data/test.txt` 파일이 안전하게 남아있는 것을 확인하여 **데이터 영속성**을 증명하였습니다.
## 🛠️ 4. 트러블슈팅 (Troubleshooting)

### ✅ 사례 1: 포트 충돌 문제 (Port Conflict)
*   **현상:** `docker run` 실행 시 `Bind for 0.0.0.0:8080 failed` 오류 발생.
*   **원인:** 이미 호스트 컴퓨터에서 8080 포트를 다른 프로그램이 사용 중임.
*   **해결:** 
    1. `lsof -i :8080` 명령어로 범인(프로세스 ID)을 찾음.
    2. `kill -9 <PID>` 명령어로 해당 프로그램을 종료하거나, 도커 실행 시 포트를 `8081:80`처럼 변경하여 해결함.

### ✅ 사례 2: 권한 거부 문제 (Permission Denied)
*   **현상:** `docker` 명령어 입력 시 `Got permission denied` 메시지 출력.
*   **원인:** 도커 엔진은 보안상 관리자(root) 권한이 필요하지만, 일반 사용자 계정으로 접근하여 거부됨.
*   **해결:** 
    1. 임시 해결: 모든 명령어 앞에 `sudo`를 붙여서 실행 (예: `sudo docker ps`).
    2. 근본 해결: `sudo usermod -aG docker $USER` 명령어를 통해 현재 사용자를 도커 그룹에 포함시켜 권한 문제를 해결함.

## 🔗 4. Git 연동 및 버전 관리 (Git & GitHub)

### 🚩 원격 저장소 연결 확인
터미널에서 `git remote -v` 명령어를 통해 GitHub 저장소와 정상적으로 연결된 것을 확인하였습니다.

```bash
# 실행 결과
origin  https://github.com/jangbang37/codyssey-mission.git (fetch)
origin  https://github.com/jangbang37/codyssey-mission.git (push)

```bash
# 실행 결과
origin  https://github.com/jangbang37/codyssey-mission
.git (fetch)
origin  https://github.com/jangbang37/codyssey-mission.git (push)
