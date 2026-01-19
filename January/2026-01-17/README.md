# 2026-01-17 학습 노트

## 📌 작업 요약
모바일 앱의 온라인/오프라인 연결 상태 관리 및 실시간 알림 시스템 개선

---

## 🔧 작업 내용

### 1. 소켓 연결 상태 관리
- React Native 앱에서 WebSocket 연결 상태를 실시간으로 추적
- 연결 중(connecting), 온라인(online), 오프라인(offline) 상태 구분
- SocketContext를 통한 전역 상태 관리

### 2. 실시간 알림 컴포넌트 개선
- RealtimeNotification 컴포넌트 업데이트
- 연결 상태에 따른 UI 피드백 제공
- 사용자에게 네트워크 상태 시각적 표시

### 3. 여러 화면에 연결 상태 처리 적용
- BandSearchScreen: 밴드 검색 화면
- NerveStimScreen: 신경자극 화면
- ReportScreen: 리포트 화면
- UserManageScreen: 사용자 관리 화면

### 4. API 서비스 계층 개선
- 연결 상태에 따른 API 호출 처리
- 오프라인 시 적절한 에러 핸들링

---

## 💡 학습한 기술 개념

### React Native Context API
- Context를 통한 전역 상태 관리
- Provider 패턴으로 하위 컴포넌트에 상태 전달
- 여러 화면에서 동일한 소켓 연결 상태 공유

### WebSocket 연결 관리
- 연결 생명주기 관리 (연결, 재연결, 종료)
- 연결 상태 감지 및 이벤트 핸들링
- 네트워크 불안정 시 재연결 로직

### React Native 상태 피드백
- 사용자에게 네트워크 상태 실시간 표시
- 로딩 상태, 연결 상태, 에러 상태 UI 구분
- 사용자 경험(UX) 개선을 위한 시각적 피드백

---

## 🐛 트러블슈팅

### 문제: 연결 중 상태에서 팝업이 계속 표시됨
**원인:**
- 연결 상태 전환이 완료되지 않았는데 UI가 먼저 업데이트됨
- 상태 변경 타이밍 이슈

**해결 개념:**
- 연결 상태를 명확히 구분 (connecting, connected, disconnected)
- 상태 전환 시 적절한 딜레이 및 조건 체크
- 팝업 표시 로직에 상태 조건 추가

---

## 🔍 핵심 학습 포인트

1. **모바일 앱에서의 실시간 통신**
   - WebSocket을 통한 양방향 통신
   - 모바일 환경에서의 네트워크 불안정성 대응

2. **React Native Context 패턴**
   - 전역 상태 관리의 효율적 방법
   - Props drilling 방지

3. **사용자 경험(UX) 개선**
   - 연결 상태에 따른 적절한 피드백 제공
   - 오프라인 상황 대응

4. **상태 관리 베스트 프랙티스**
   - 명확한 상태 구분
   - 상태 전환 로직 최적화

---

## 📚 구글링 키워드
- "React Native WebSocket connection management"
- "React Native Context API global state"
- "WebSocket reconnection strategy"
- "React Native network state handling"
- "Mobile app offline mode UX"

---

## 🎯 다음에 공부할 것
- WebSocket 재연결 전략 (exponential backoff)
- React Native NetInfo API 활용
- 오프라인 모드에서의 데이터 캐싱
- Redux vs Context API 성능 비교
