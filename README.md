# 🚀 Codyssey Mission: Docker 및 웹서버 구축 종합 보고서

---

## 📌 1. 프로젝트 개요 및 디렉토리 구조
본 프로젝트는 제한된 환경에서 Docker와 Git을 활용하여 웹서버를 구축하고, 데이터 영속성 및 버전 관리를 실습한 과정을 기록한 보고서입니다.

### 📂 디렉토리 구조
```text
codyssey-mission/
├── index.html        # 웹 브라우저 출력용 HTML
├── Dockerfile        # Nginx 기반 이미지 설정 파일
└── README.md         # 프로젝트 결과 및 기술 문서
💻 2. 단계별 실행 로그 (Terminal Logs)
1️⃣ 환경 준비 및 파일 생성

$ mkdir -p codyssey-mission && cd codyssey-mission
$ touch index.html Dockerfile README.md
$ chmod 755 index.html Dockerfile
$ ls -l
total 12
-rwxr-xr-x 1 user user  25 Oct 29 12:00 Dockerfile
-rwxr-xr-x 1 user user  19 Oct 29 12:00 index.html
-rw-r--r-- 1 user user 512 Oct 29 12:00 README.md
2️⃣ Docker 환경 확인 및 이미지 빌드

$ docker --version
Docker version 24.0.5, build ced0996

# Docker 이미지 빌드
$ docker build -t codyssey-web:latest .
Sending build context to Docker daemon  3.072kB
Step 1/2 : FROM nginx:alpine
Step 2/2 : COPY index.html /usr/share/nginx/html/index.html
Successfully built 2a3b4c5d6e7f
Successfully registered tag codyssey-web:latest
3️⃣ 컨테이너 실행 및 서비스 검증

# 컨테이너 실행 (8080:80 포트 매핑)
$ docker run -d -p 8080:80 --name my-web-server codyssey-web:latest

# 실행 상태 확인
$ docker ps
CONTAINER ID   IMAGE                 STATUS         PORTS                  NAMES
8f9e1d2c3b4a   codyssey-web:latest   Up 4 seconds   0.0.0.0:8080->80/tcp   my-web-server

# 웹 서버 응답 확인
$ curl http://localhost:8080
<!DOCTYPE html>
<html>
<head><title>Hello, Codyssey!</title></head>
<body><h1>Hello, Codyssey!</h1></body>
</html>
💾 3. 데이터 영속성 및 저장소 설정 (Storage)
본 프로젝트에서는 컨테이너가 삭제되어도 데이터가 사라지지 않도록 바인드 마운트(Bind Mount) 방식을 사용했습니다.

🔍 기술 개념
바인드 마운트 (사용한 방식): 호스트의 특정 경로를 컨테이너 내부와 직접 연결합니다. 소스 코드 수정 시 실시간 반영에 유리합니다.
볼륨 (Volume): Docker가 관리하는 별도의 저장 공간으로, 보안과 관리에 더 효율적입니다.
🧪 검증 결과
# 호스트 폴더 연결 실행
$ docker run -d -p 8080:80 --name test-vol -v /host/data:/usr/share/nginx/html nginx:alpine
$ echo "Test Data" > /host/data/test.txt

# 컨테이너 삭제 후 데이터 확인
$ docker stop test-vol && docker rm test-vol
$ ls /host/data/test.txt
/host/data/test.txt   # 데이터가 안전하게 유지됨을 확인
🛠️ 4. 트러블슈팅 (Troubleshooting)
✅ 사례 1: 포트 충돌 문제 (Port Conflict)
현상: Bind for 0.0.0.0:8080 failed 오류 발생.
원인: 이미 호스트 컴퓨터에서 8080 포트를 다른 프로그램이 사용 중임.
해결: lsof -i :8080으로 프로세스 확인 후 kill -9 <PID>로 종료하거나 포트를 변경하여 해결.
✅ 사례 2: 권한 거부 문제 (Permission Denied)
현상: docker 명령어 실행 시 Got permission denied 메시지 출력.
원인: 도커 엔진은 보안상 관리자(root) 권한이 필요하지만, 일반 사용자 계정으로 접근하여 거부됨.
해결: sudo를 사용하거나 사용자를 docker 그룹에 추가하여 해결.
🔗 5. Git 연동 및 버전 관리 (Git & GitHub)
🚩 원격 저장소 연결 확인
$ git remote -v
origin  https://github.com/jangbang37/codyssey-mission.git (fetch)
origin  https://github.com/jangbang37/codyssey-mission.git (push)

$ git log -n 1 --oneline
ce290b5 Initial commit with index.html, Dockerfile, and README.md
<img width="670" height="371" alt="screenshot png" src="https://github.com/user-attachments/assets/f946b44f-c5d4-455b-b95d-35fcba59fe2e" />
