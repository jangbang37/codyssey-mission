# 🚀 Codyssey Mission: Docker 및 웹서버 구축 종합 보고서

---

## 📌 1. 프로젝트 개요 및 디렉토리 구조
본 프로젝트는 Docker와 Git을 활용하여 웹서버를 구축하고, 데이터 영속성 및 버전 관리를 실습한 과정을 기록한 보고서입니다.

### 📂 디렉토리 구조 및 구성 이유 (평가 항목 #10)
- `index.html`: 사용자에게 보여줄 메인 웹 페이지입니다.
- `Dockerfile`: Nginx 이미지를 기반으로 커스텀 이미지를 생성하기 위한 설계도입니다.
- `README.md`: 프로젝트의 전 과정과 기술적 근거를 기록한 문서입니다.
- **선택 근거:** 최소한의 파일로 웹 서비스를 구동하고, 관리가 용이하도록 루트 디렉토리에 배치하였습니다.

---

## 💻 2. 단계별 실행 로그 (Terminal Logs)

### 1️⃣ 환경 준비 및 권한 설정 (평가 항목 #2, #3, #15)
- **Docker 버전 확인:** `$ docker --version` (Docker version 24.0.5)
- **권한 설정 원리:** `chmod 755`는 소유자에게 읽기/쓰기/실행(7), 그룹/기타 사용자에게 읽기/실행(5) 권한을 부여합니다. 웹 서버가 파일을 읽기 위해 필수적인 설정입니다.

```bash
$ chmod 755 index.html Dockerfile
$ ls -l
total 12
-rwxr-xr-x 1 user user  25 Oct 29 12:00 Dockerfile
-rwxr-xr-x 1 user user  19 Oct 29 12:00 index.html
-rw-r--r-- 1 user user 512 Oct 29 12:00 README.md
```
