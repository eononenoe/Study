# 2026-01-14 학습 노트

## 📌 작업 요약
모바일 앱 지도 기능 추가 및 UI 개선

---

## 🔧 작업 내용

### 1. 지도 기능 추가
- BandDetailModal에 지도 기능 통합
- BandSearchScreen에 위치 기반 검색 기능 확장

### 2. UI 개선
- DashboardScreen 레이아웃 개선
- AlertItem 컴포넌트 업데이트
- 테마 색상 및 스타일 조정 (theme.js)

### 3. 패키지 관리
- 지도 관련 라이브러리 추가
- 의존성 업데이트 (package.json)

---

## 💡 학습한 기술 개념

### React Native 지도 라이브러리
- react-native-maps 또는 유사 라이브러리 사용
- 모바일 환경에서의 지도 렌더링
- 위치 정보 표시 및 인터랙션

### 모달과 지도 통합
- 모달 컴포넌트 내에서 지도 표시
- 성능 최적화 (지도 로딩)
- 사용자 인터랙션 처리

### React Native 레이아웃
- 반응형 디자인
- Flexbox를 활용한 레이아웃 구성
- 모바일 화면 크기 대응

---

## 🐛 트러블슈팅

### 문제: 지도가 모달에서 제대로 렌더링되지 않음
**원인:**
- 모달의 생명주기와 지도 초기화 타이밍 불일치
- 지도 컴포넌트가 마운트되기 전에 좌표 설정 시도

**해결 개념:**
- 모달이 완전히 열린 후 지도 초기화
- useEffect 훅을 사용한 생명주기 관리
- 조건부 렌더링으로 지도 로딩 타이밍 제어

---

## 🔍 핵심 학습 포인트

1. **모바일 지도 렌더링**
   - 네이티브 지도 컴포넌트 활용
   - 성능 고려사항
   - 위치 권한 처리

2. **React Native 컴포넌트 생명주기**
   - useEffect를 통한 사이드 이펙트 관리
   - 모달과 하위 컴포넌트 간 생명주기 조율

3. **위치 기반 서비스**
   - GPS 좌표 처리
   - 지도상에 마커 표시
   - 사용자 위치 추적

4. **UI/UX 개선**
   - 직관적인 지도 인터페이스
   - 알림 컴포넌트 시각화
   - 일관된 테마 적용

---

## 📚 구글링 키워드
- "React Native Maps tutorial"
- "react-native-maps modal integration"
- "React Native geolocation"
- "React Native useEffect lifecycle"
- "Mobile map performance optimization"
- "React Native Flexbox layout"

---

## 🎯 다음에 공부할 것
- 지도 클러스터링 (마커가 많을 때)
- 오프라인 지도 캐싱
- 커스텀 마커 디자인
- 지도 애니메이션 효과
- 위치 기반 알림 시스템
