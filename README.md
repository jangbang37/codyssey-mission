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
