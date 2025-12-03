# 2025-11-27 학습 노트

## 프로젝트 개요
제조업 생산 데이터 분석 시스템 개발 (Flask + Vue.js)

---

## 1. 트러블슈팅

### 문제 1: 전일대비 계산 로직 오류
**문제 상황**
- 사용자가 기간(10.27~11.26)을 선택했는데, 전체 기간 합계와 하루치 데이터를 비교하는 오류
- 898,968개(전체 합계) - 100,590개(하루치) = +798,378개로 잘못 계산됨

**원인 분석**
- 전일대비는 "선택 기간의 마지막 날짜"와 "그 전날"을 비교해야 함
- 전체 기간 데이터를 DB에서 가장 최근 날짜와 비교하는 로직 작성
- 날짜 범위와 단일 날짜 데이터를 혼동

**해결 개념**
- 날짜 필터링을 두 번 수행: 기간 전체 조회 + 특정 날짜 조회
- 선택 기간의 end_date와 end_date-1일을 각각 조회
- 두 날짜의 하루치 데이터만 비교하도록 로직 분리

**핵심 학습**
- 통계에서 "전일대비"는 항상 동일한 단위(둘 다 하루치)를 비교해야 함
- 날짜 기반 집계 시 범위 쿼리와 특정 날짜 쿼리를 명확히 구분
- 사용자 요구사항의 정확한 이해가 중요 (전체 vs 마지막 날짜)

**구글링 키워드**
- "Flask SQLAlchemy filter by specific date"
- "date range vs single date query"
- "day-over-day comparison best practices"

---

### 문제 2: 이메일 발송 시 401 UNAUTHORIZED 에러
**문제 상황**
- 이메일 발송 버튼 클릭 시 "세션이 만료되었습니다" 메시지
- 실제로는 SMTP 인증 실패인데 JWT 토큰 만료로 오해

**원인 분석**
- 백엔드 엔드포인트에 JWT 인증 데코레이터 적용
- SMTP 인증 실패 시 401 상태 코드 반환
- 프론트엔드 axios interceptor가 모든 401을 JWT 만료로 처리
- 두 가지 다른 인증(JWT, SMTP)을 혼동

**해결 개념**
- JWT 인증과 SMTP 인증은 별개의 레이어
- 이메일 발송은 SMTP 서버 인증으로 충분하므로 JWT 제거
- HTTP 상태 코드 구분: JWT 인증 실패(401), SMTP 인증 실패(400)
- 에러 유형별로 다른 상태 코드 반환

**핵심 학습**
- 인증(Authentication)과 권한(Authorization)의 레이어 분리
- HTTP 상태 코드의 의미론적 사용 중요성
- 클라이언트 에러 핸들링 시 상태 코드 컨벤션 준수
- 하나의 API에서 여러 인증 방식이 충돌할 수 있음

**구글링 키워드**
- "JWT authentication vs SMTP authentication"
- "HTTP status code best practices 401 vs 400"
- "Flask remove authentication decorator"
- "axios interceptor multiple error types"

---

### 문제 3: 다중 이메일 제공자 지원
**문제 상황**
- Gmail만 지원하도록 하드코딩된 SMTP 설정
- 네이버, 다음 사용자는 이메일 발송 불가

**원인 분석**
- SMTP 서버 주소와 포트가 이메일 제공자마다 다름
- SSL과 TLS 방식도 제공자마다 상이
- 네이버는 IMAP/SMTP 설정을 수동으로 켜야 함

**해결 개념**
- 이메일 도메인 파싱으로 제공자 자동 감지
- 설정 딕셔너리로 제공자별 SMTP 정보 관리
- SSL(465 포트)과 TLS(587 포트) 분기 처리
- 사용자에게 제공자별 설정 방법 안내

**핵심 학습**
- 외부 서비스 연동 시 다중 제공자 지원 고려
- 설정값을 하드코딩하지 않고 구조화
- 사용자 경험 개선: 자동 감지 + 명확한 안내
- SMTP 프로토콜의 SSL/TLS 차이점

**구글링 키워드**
- "SMTP server settings by email provider"
- "Python smtplib SSL vs TLS"
- "Naver mail SMTP settings"
- "Gmail app password generation"

---

### 문제 4: 엑셀 데이터 파싱 시 잘못된 컬럼 감지
**문제 상황**
- 불량 데이터가 149,148,715건으로 비정상적으로 높음
- 금액 데이터(목표금액, 매출금액)가 불량 컬럼으로 인식됨

**원인 분석**
- 단순 위치 기반 컬럼 감지 (20번째 이후 = 불량)
- 엑셀 구조 변경 시 금액/운영 데이터까지 불량으로 인식
- 컬럼명 기반 필터링 부재

**해결 개념**
- 키워드 기반 필터링: 제외 키워드 + 포함 키워드
- 제외: 금액, 목표, Array, 검사수, 작업자, 시간 등
- 포함: 불량, 파손, 기포, 도포, 이물 등 실제 불량 유형
- 이중 필터링으로 정확도 향상

**핵심 학습**
- 동적 데이터 파싱 시 규칙 기반 접근의 한계
- 키워드 화이트리스트/블랙리스트 패턴
- 데이터 검증의 중요성 (이상치 탐지)
- 도메인 지식을 코드에 반영하는 방법

**구글링 키워드**
- "dynamic column detection pandas"
- "keyword-based data filtering"
- "data validation best practices"
- "outlier detection in data processing"

---

## 2. 새로 배운 기술

### Flask 백엔드
- **JWT 인증 데코레이터의 선택적 사용**: 모든 API가 JWT를 필요로 하지 않음
- **HTTP 상태 코드 전략**: 401(인증 실패), 400(잘못된 요청) 구분
- **날짜 필터링**: SQLAlchemy에서 특정 날짜와 날짜 범위 쿼리의 차이

### SMTP 이메일 발송
- **다중 제공자 지원 패턴**: 도메인 파싱 + 설정 딕셔너리
- **SSL vs TLS**: SMTP_SSL(465)과 starttls()(587)의 차이
- **인증 방식의 다양성**: Gmail 앱 비밀번호 vs 네이버 일반 비밀번호

### 데이터 처리
- **동적 컬럼 감지**: 위치 기반 vs 키워드 기반
- **데이터 검증**: 이상치 탐지로 로직 오류 발견
- **필터링 전략**: 제외 키워드 + 포함 키워드 이중 검증

### Vue.js 프론트엔드
- **Axios Interceptor 설계**: 전역 에러 핸들링의 장단점
- **사용자 경험**: 에러 메시지의 명확성이 중요
- **조건부 렌더링**: 이메일 제공자별 다른 안내 표시

---

## 3. 오늘의 핵심 교훈

### 1. 요구사항의 정확한 이해
"전일대비"라는 단어가 "DB 전체의 마지막 날짜"인지 "선택 기간의 마지막 날짜"인지 명확히 확인 필요

### 2. 인증 레이어의 분리
JWT 인증은 API 접근 제어용, SMTP 인증은 이메일 서버 접근용으로 목적이 다름을 인식

### 3. 다중 제공자 지원의 중요성
특정 서비스에 종속되지 않도록 처음부터 확장 가능하게 설계

### 4. 데이터 검증의 필요성
비정상적인 결과값은 로직 오류의 신호. 데이터 검증을 통해 조기 발견

### 5. 에러 메시지의 명확성
"세션 만료"보다 "이메일 인증 실패"가 사용자에게 훨씬 유용한 정보

---

## 4. 추천 학습 자료

### 공식 문서
- [Flask-JWT-Extended Documentation](https://flask-jwt-extended.readthedocs.io/)
- [Python smtplib Official Docs](https://docs.python.org/3/library/smtplib.html)
- [SQLAlchemy Date/Time Functions](https://docs.sqlalchemy.org/en/14/core/functions.html)
- [Axios Interceptors Guide](https://axios-http.com/docs/interceptors)

### 검색 키워드
- "Flask authentication vs authorization"
- "SMTP protocol SSL TLS difference"
- "SQLAlchemy date filtering best practices"
- "axios global error handling"
- "dynamic column detection pandas"
- "HTTP status codes REST API"

### 학습 추천
- SMTP 프로토콜 깊이 이해하기
- SQLAlchemy 날짜/시간 쿼리 최적화
- HTTP 상태 코드 완벽 가이드
- 데이터 검증 패턴 (Schema validation)

---

## 5. 회고

### 잘한 점
- 사용자 피드백을 즉시 반영하여 요구사항 정확히 파악
- 에러의 근본 원인을 찾아 구조적으로 해결
- 다중 제공자 지원으로 확장성 확보

### 개선할 점
- 처음부터 요구사항을 더 명확히 확인할 것
- 데이터 검증을 더 일찍 추가할 것
- 인증 레이어 설계 시 더 신중하게 고민할 것

### 배운 점
- 사용자가 말하는 "전일"이 무엇을 의미하는지 정확히 확인 필요
- 하나의 API에서 여러 인증 방식이 충돌할 수 있음을 인식
- 외부 서비스 연동 시 다중 제공자를 처음부터 고려
