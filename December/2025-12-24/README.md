# 2025-12-24 학습 노트: 템플릿 다운로드 기능 구현

## 트러블슈팅

### 1. axios baseURL과 URL 중복 문제 (/api/api/)

**문제 상황**
- 템플릿 다운로드 요청 시 `/api/api/client-plan/download-template/...` 형태로 URL이 중복되는 현상
- 404 Not Found 에러 발생

**원인 분석**
- axios 인스턴스 설정에서 baseURL이 이미 `/api`를 포함
- 코드에서 요청 URL을 `/api/client-plan/...`로 작성하여 중복 발생
- axios는 baseURL + 요청 URL을 자동으로 결합함

**해결 개념**
- axios baseURL 설정 이해: baseURL은 모든 요청에 자동으로 앞에 붙는 prefix
- 요청 URL은 baseURL 이후의 상대 경로만 작성
- 다른 API 엔드포인트와 일관성 있게 상대 경로만 사용

**핵심 학습 포인트**
- axios 인스턴스 설정과 요청 URL의 관계 이해
- API 클라이언트 라이브러리의 baseURL 개념
- URL 구성 시 중복 방지를 위한 상대/절대 경로 구분

**구글링 키워드**
- "axios baseURL duplicate api"
- "axios relative vs absolute path"
- "axios instance configuration"

---

### 2. 서버 환경과 로컬 환경 파일 경로 차이

**문제 상황**
- 서버에 템플릿 파일을 업로드했지만 다운로드 실패
- 로컬 개발 환경과 서버 환경의 파일 경로가 다름

**원인 분석**
- 서버: Linux 환경, 절대 경로 사용 가능
- 로컬: Windows 환경, 다른 파일 시스템 구조
- 환경 감지 로직의 복잡성

**해결 개념**
- 서버 환경에서는 절대 경로 하드코딩 사용
- 기존에 작동하는 코드(ALC 다운로드)와 동일한 패턴 적용
- 단순성과 명확성을 위해 환경 분기 로직 제거

**핵심 학습 포인트**
- 환경별 설정 관리 방법
- 프로덕션 환경에서는 명확한 절대 경로 사용이 더 안전할 수 있음
- 기존 코드 패턴 참조의 중요성

**구글링 키워드**
- "Flask file path production vs development"
- "Python os.path cross platform"
- "Flask send_file absolute path"

---

### 3. Flask send_file을 사용한 파일 다운로드

**문제 상황**
- 서버에 있는 엑셀 파일을 브라우저로 다운로드 제공
- 파일명을 동적으로 변경하여 제공 필요

**해결 개념**
- Flask의 send_file 함수 사용
- as_attachment=True로 다운로드 강제
- download_name 파라미터로 다운로드 파일명 지정
- responseType: 'blob'로 바이너리 데이터 수신
- Blob API로 브라우저에서 파일 다운로드 트리거

**핵심 학습 포인트**
- 서버에서 파일을 클라이언트로 전송하는 방법
- MIME 타입 설정의 중요성
- 브라우저 Blob API를 활용한 파일 다운로드

**구글링 키워드**
- "Flask send_file download_name"
- "Flask serve file download"
- "axios blob responseType download"
- "JavaScript Blob download file"

---

### 4. 프론트엔드 빌드와 배포 캐싱 문제

**문제 상황**
- 코드를 수정했지만 브라우저에서 변경사항이 반영되지 않음
- 빌드를 새로 했는데도 이전 코드가 실행됨

**원인 분석**
- 브라우저 캐싱
- 서버측 빌드 파일 미배포
- 개발 서버 재시작 필요

**해결 개념**
- npm run build로 프로덕션 빌드 생성
- 빌드된 파일을 서버에 배포
- 브라우저 하드 리프레시(Ctrl + Shift + R)
- 백엔드 서버 재시작 필요성

**핵심 학습 포인트**
- 프론트엔드 개발과 프로덕션 배포의 차이
- 캐싱이 개발 과정에 미치는 영향
- 코드 변경 후 반영 확인 프로세스

**구글링 키워드**
- "Vue build production deployment"
- "browser cache clear hard refresh"
- "npm run build not updating"

---

## 배운 기술

### Flask 백엔드

**send_file 함수**
- 서버 파일을 HTTP 응답으로 전송
- as_attachment: 다운로드로 처리할지 여부
- download_name: 다운로드 시 파일명 (Flask 2.0+)
- mimetype: 파일 타입 지정

**Blueprint 라우팅**
- 동적 URL 파라미터 사용
- 여러 템플릿 타입을 하나의 엔드포인트로 처리

### axios (HTTP 클라이언트)

**baseURL 설정**
- axios 인스턴스의 기본 URL prefix
- 모든 요청에 자동으로 앞에 붙음
- 상대 경로와 결합되는 방식 이해

**responseType: 'blob'**
- 바이너리 데이터(파일) 수신 시 필수
- Blob 객체로 응답 받음

### 브라우저 Blob API

**파일 다운로드 구현**
- Blob 객체 생성
- URL.createObjectURL()로 임시 URL 생성
- a 태그를 동적으로 생성하여 클릭
- URL.revokeObjectURL()로 메모리 해제

---

## 핵심 교훈

### 1. API 클라이언트 설정 이해의 중요성
axios 같은 HTTP 클라이언트의 baseURL 설정을 정확히 이해하지 못하면 URL 중복 같은 문제 발생. 라이브러리 설정을 먼저 확인하고 사용해야 함.

### 2. 기존 코드 패턴 참조
새로운 기능 구현 시 비슷한 기능이 이미 구현되어 있다면 그 패턴을 따르는 것이 안전. ALC 다운로드 기능을 참조하여 문제 해결.

### 3. 환경 차이 고려
개발 환경과 프로덕션 환경의 차이(파일 경로, 포트, 설정 등)를 항상 염두에 두고 개발. 단순한 방법이 복잡한 환경 분기보다 나을 수 있음.

### 4. 디버깅 시 전체 흐름 파악
프론트엔드에서 백엔드까지의 전체 요청 흐름을 이해해야 문제 위치 파악 가능. 브라우저 개발자 도구의 Network 탭 활용.

---

## 추천 학습 자료

### 공식 문서
- Flask send_file: https://flask.palletsprojects.com/en/latest/api/#flask.send_file
- axios Configuration: https://axios-http.com/docs/config_defaults
- MDN Blob API: https://developer.mozilla.org/en-US/docs/Web/API/Blob
- MDN File Download: https://developer.mozilla.org/en-US/docs/Web/API/HTMLAnchorElement/download

### 검색 키워드
- "Flask file download best practices"
- "axios interceptors baseURL"
- "JavaScript programmatic file download"
- "Vue production build deployment"
- "HTTP Content-Disposition header"

### 추가 학습 주제
- Flask 파일 업로드/다운로드 보안
- axios 인터셉터 활용
- 대용량 파일 스트리밍
- 파일 다운로드 진행률 표시
- CORS 설정과 파일 다운로드
