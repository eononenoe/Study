# 2026-01-16 학습 노트

## 📌 작업 요약
백엔드 대규모 리팩토링 및 모듈화, Docker 컨테이너화, 테스트 코드 작성

---

## 🔧 작업 내용

### 1. 백엔드 API 모듈화
- 단일 파일에 있던 API 라우터를 기능별로 분리
  - auth.py: 인증/인가 관련
  - bands.py: 밴드 관리
  - dashboard.py: 대시보드 데이터
  - events.py: 이벤트 처리
  - nervestim.py: 신경자극 관련

### 2. 데이터베이스 계층 분리
- models.py: SQLAlchemy 모델 정의
- database.py: 데이터베이스 연결 및 세션 관리
- service 계층: 비즈니스 로직 분리
  - query.py: 쓰기 작업
  - select.py: 읽기 작업

### 3. MQTT 클라이언트 분리
- mqtt_client.py: MQTT 클라이언트 별도 파일로 분리
- mqtt_nervestim.py: 신경자극 관련 MQTT 처리

### 4. 인프라 및 유틸리티
- Dockerfile 작성: 컨테이너화
- scheduler.py: 스케줄링 작업
- socket_handlers.py: WebSocket 이벤트 핸들러
- utils.py: 공통 유틸리티 함수

### 5. 테스트 코드 작성
- pytest 설정 (pytest.ini, conftest.py)
- test_auth.py: 인증 테스트
- test_bands.py: 밴드 관련 테스트
- test_utils.py: 유틸리티 함수 테스트

### 6. 모바일 앱 컴포넌트 추가
- AppHeader: 앱 헤더 컴포넌트
- RealtimeNotification: 실시간 알림
- DashboardScreen: 대시보드 화면

---

## 💡 학습한 기술 개념

### Flask 애플리케이션 구조화
- Blueprint를 활용한 API 라우터 모듈화
- 계층형 아키텍처 (Controller - Service - Repository)
- 관심사의 분리 (Separation of Concerns)

### SQLAlchemy ORM
- 모델 정의 및 관계 설정
- 세션 관리 및 트랜잭션 처리
- 서비스 계층 패턴으로 비즈니스 로직 분리

### Docker 컨테이너화
- Dockerfile 작성
- Python 애플리케이션 컨테이너화
- 의존성 관리 (requirements.txt)

### 테스트 주도 개발 (TDD)
- pytest 프레임워크 사용
- fixture를 통한 테스트 환경 설정
- 단위 테스트 작성 패턴

### MQTT 프로토콜
- IoT 디바이스와의 메시지 통신
- Pub/Sub 패턴
- 메시지 브로커 연동

---

## 🐛 트러블슈팅

### 문제: MQTT 연결 중 팝업이 나타나지 않음
**원인:**
- MQTT 연결 상태 변경 시 이벤트가 제대로 전달되지 않음
- 클라이언트와 서버 간 상태 동기화 이슈

**해결 개념:**
- MQTT 클라이언트를 별도 파일로 분리하여 생명주기 관리 개선
- WebSocket을 통한 실시간 상태 업데이트
- 이벤트 핸들러를 socket_handlers.py로 분리하여 명확한 책임 부여

---

## 🔍 핵심 학습 포인트

1. **모듈화의 중요성**
   - 코드 유지보수성 향상
   - 테스트 용이성 증가
   - 팀 협업 시 충돌 최소화

2. **계층형 아키텍처**
   - API 계층 (라우터)
   - 비즈니스 로직 계층 (서비스)
   - 데이터 접근 계층 (리포지토리)

3. **테스트 코드의 필요성**
   - 리팩토링 시 안전성 보장
   - 버그 조기 발견
   - 문서화 효과

4. **Docker 컨테이너화**
   - 환경 일관성 보장
   - 배포 용이성
   - 의존성 격리

5. **MQTT vs WebSocket**
   - MQTT: IoT 디바이스 통신에 최적화
   - WebSocket: 웹/모바일 클라이언트와 실시간 통신

---

## 📚 구글링 키워드
- "Flask Blueprint modularization"
- "SQLAlchemy session management best practices"
- "Python pytest fixtures"
- "Docker Python application"
- "MQTT Python paho client"
- "Flask WebSocket SocketIO"
- "Service layer pattern Python"
- "Repository pattern SQLAlchemy"

---

## 🎯 다음에 공부할 것
- Docker Compose로 멀티 컨테이너 구성
- CI/CD 파이프라인 구축
- 통합 테스트 (Integration Test) 작성
- API 문서화 (Swagger/OpenAPI)
- 로깅 및 모니터링 시스템
- 데이터베이스 마이그레이션 도구 (Alembic)
