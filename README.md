\# Codyssey Mission



간단한 HTML 페이지를 Nginx 컨테이너로 실행하는 프로젝트입니다.



\## 파일

\- `index.html`

\- `Dockerfile`



\## 실행 방법

```bash

docker build -t codyssey-mission .

docker run -d -p 8080:80 codyssey-mission

