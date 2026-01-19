# 2026-01-13 학습 노트

## 📌 작업 요약
프로젝트 초기 세팅 및 전체 구조 구축 (백엔드 Flask + 프론트엔드 Vue.js + 모바일 React Native)

---

## 🔧 작업 내용 (시간순)

### 16:15 - Initial commit
- Git 저장소 초기화
- README.md 생성

### 16:20 - 초기 세팅전
#### 백엔드 (Flask)
- Flask 애플리케이션 구조 생성
- API 엔드포인트 구현
  - 밴드 관리 (api_band.py)
  - 혈압 측정 (api_bloodpressure.py)
  - 디바이스 관리 (api_device.py)
  - 신경자극 (api_nervestim.py)
  - 리포트 생성 (api_report.py)
- MQTT 프로토콜 연동 (mqtt.py)
- 데이터베이스 설계 및 구현
  - SQLAlchemy ORM 사용
  - 밴드, 헬스 테이블 정의
  - 쿼리 및 서비스 계층 분리
- SMS 발송 기능 (SOAP API 연동)
- 웹 크롤링 기능
- 데이터베이스 마이그레이션 파일 작성

#### 프론트엔드 (Vue.js)
- Vue 3 + Vuex 프로젝트 구조 생성
- 스토어 모듈 구현
  - authStore: 인증 관리
  - dashboardStore: 대시보드 데이터
  - deviceStore: 디바이스 관리
  - bloodPressureStore: 혈압 데이터
  - nerveStimStore: 신경자극 데이터
  - reportStore: 리포트 데이터
- 페이지 컴포넌트 작성
  - Dashboard, Band, Device, User
  - Login, BloodPressure, NerveStim, Report
- Leaflet 지도 플러그인 통합
- Socket.io 클라이언트 연동
- API 서비스 계층 구현

#### 기타
- HTML 미리보기 파일 작성
- 로거 설정
- requirements.txt 작성

### 19:00 - UI 대충?
- HTML 미리보기 파일 3개 삭제 (불필요)
- README.md 대폭 업데이트
- Python 캐시 파일 생성
- requirements.txt 수정

### 22:20 - 모바일앱 구조
#### React Native 앱 전체 구조
- Expo 기반 프로젝트 설정
- 주요 화면 구현
  - LoginScreen: 로그인
  - DashboardScreen: 대시보드
  - BandSearchScreen: 밴드 검색
  - UserManageScreen: 사용자 관리
  - NerveStimScreen: 신경자극 제어
  - ReportScreen: 리포트 조회
  - BloodPressureScreen: 혈압 측정
  - DeviceScreen: 디바이스 관리
  - SettingsScreen: 설정
- Context API 기반 상태 관리
  - AuthContext: 인증 상태
  - BandContext: 밴드 데이터
  - DashboardContext: 대시보드 데이터
  - SocketContext: WebSocket 연결
- 재사용 가능한 컴포넌트
  - AppHeader: 헤더
  - AlertItem: 알림 아이템
  - BandListItem: 밴드 리스트 아이템
  - BandDetailModal: 밴드 상세 모달
  - StatCard: 통계 카드
  - VitalCard: 바이탈 카드
  - QuickActionButton: 빠른 액션 버튼
- 서비스 계층 (API, Socket)
- 네비게이션 구조
- 반응형 유틸리티
- 테마 시스템

---

## 💡 학습한 기술 개념

### Flask 백엔드 개발
- Flask 프레임워크 구조
- RESTful API 설계
- SQLAlchemy ORM 패턴
- MQTT IoT 통신 프로토콜
- WebSocket (Socket.io) 실시간 통신
- SOAP API 연동

### Vue.js 프론트엔드 개발
- Vue 3 Composition API
- Vuex 상태 관리 패턴
- Vue Router 라우팅
- Axios HTTP 클라이언트
- Leaflet 지도 라이브러리
- Socket.io 클라이언트

### React Native 모바일 개발
- Expo 프레임워크
- React Navigation
- Context API 전역 상태 관리
- React Native 컴포넌트
- 반응형 디자인 (Dimensions API)
- 모바일 앱 아키텍처

### 프로젝트 아키텍처
- 풀스택 개발 구조 (백엔드 + 웹 + 모바일)
- RESTful API 설계
- 실시간 통신 아키텍처
- IoT 디바이스 연동
- 데이터베이스 스키마 설계

---

## 🔍 핵심 학습 포인트

1. **풀스택 프로젝트 구조**
   - 백엔드, 프론트엔드, 모바일 앱의 역할 분리
   - API 중심 아키텍처
   - 각 계층 간 통신 방법

2. **상태 관리 패턴**
   - Vue: Vuex 스토어 모듈화
   - React Native: Context API 패턴
   - 전역 상태와 로컬 상태의 구분

3. **실시간 통신**
   - WebSocket을 통한 양방향 통신
   - MQTT를 통한 IoT 디바이스 통신
   - 이벤트 기반 아키텍처

4. **데이터베이스 설계**
   - ORM을 통한 객체-관계 매핑
   - 마이그레이션을 통한 스키마 관리
   - 서비스 계층 패턴

5. **모바일 앱 아키텍처**
   - 컴포넌트 재사용성
   - 네비게이션 구조
   - 반응형 디자인

6. **코드 구조화**
   - 계층형 아키텍처
   - 관심사의 분리
   - 모듈화

---

## 📚 구글링 키워드
- "Flask SQLAlchemy tutorial"
- "Vue 3 Vuex store modules"
- "React Native Context API"
- "Flask MQTT integration"
- "Socket.io Flask Vue integration"
- "Leaflet Vue 3"
- "React Navigation setup"
- "Flask RESTful API best practices"
- "SOAP API Python requests"
- "React Native responsive design"
- "Expo app structure"

---

## 🎯 다음에 공부할 것
- Docker를 통한 컨테이너화
- 테스트 코드 작성 (Unit Test, Integration Test)
- CI/CD 파이프라인 구축
- API 문서화 (Swagger)
- 보안 강화 (JWT, CORS, Input Validation)
- 성능 최적화
- 로깅 및 모니터링
- 에러 핸들링 표준화
- 코드 리팩토링 및 모듈화 개선
