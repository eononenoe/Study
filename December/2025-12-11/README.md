# 2025-12-11 학습 노트

## 📌 오늘 작업 요약
생산통합 조회 기능에서 데이터 그룹핑 오류, 탭 전환 버그, 수량 데이터 누락 문제 해결

---

## 🔧 트러블슈팅

### 1. Map 그룹핑 키 선택 오류 - 복합키의 필요성

**문제 상황**
- 같은 품목(item_id=1)을 여러 고객사(AL, MYT)가 주문
- 26개 데이터 행이 1개 테이블 행으로 합쳐져서 표시
- 각 고객사별 수량과 정보가 덮어써지고 마지막 고객사 데이터만 남음

**원인 분석**
- JavaScript Map에서 `item_id`만 키로 사용
- 같은 품목이라도 고객사가 다르면 별도 행으로 표시해야 하는데 단일 키로는 구분 불가
- DB에는 `(item_id, client_item_history_id)` 조합이 고유한데 프론트엔드는 item_id만 사용

**해결 개념**
- 복합키(Composite Key) 패턴 적용
- `${item_id}_${client_item_history_id}` 형태의 문자열 키 사용
- 템플릿 리터럴로 두 값을 결합하여 고유성 보장

**핵심 학습 포인트**
- Map 자료구조의 키는 데이터의 고유성을 정확히 반영해야 함
- DB 스키마의 UNIQUE 제약조건과 프론트엔드 그룹핑 로직이 일치해야 함
- 복합키가 필요한 경우 템플릿 리터럴로 문자열 키 생성 가능

**구글링 키워드**
- "JavaScript Map composite key pattern"
- "Vue grouping data by multiple fields"
- "frontend data aggregation unique key"
- "Map key with multiple values JavaScript"

---

### 2. 반응형 상태 초기화 누락으로 인한 UI 버그

**문제 상황**
- 탭 전환 시 이전 탭의 자동완성 항목이 다른 탭에도 남아있음
- "no data" 메시지가 표시되어야 하는데 이전 데이터가 계속 보임

**원인 분석**
- 조건부 early return만 사용하고 상태 초기화 누락
- `if (조건) return` 패턴에서 변수는 이전 값 유지
- Vue 반응형 시스템에서 명시적 초기화 없으면 stale data 발생

**해결 개념**
- Early return 전에 상태 초기화 먼저 수행
- `savedNumberList.value = []` 명시적 빈 배열 할당
- 모든 분기에서 상태 정리 보장

**핵심 학습 포인트**
- 반응형 상태 관리에서 명시적 초기화의 중요성
- Early return 패턴 사용 시 side effect 고려
- 조건부 로직에서도 데이터 정리(cleanup) 필수

**구글링 키워드**
- "Vue reactive data cleanup on tab change"
- "JavaScript early return side effects"
- "Vuetify autocomplete clear items programmatically"
- "Vue component state initialization best practices"

---

### 3. Vuetify v-autocomplete Props 타입 불일치

**문제 상황**
- Console에 "Invalid prop: type check failed for prop 'title'" 경고 반복 발생
- Autocomplete 동작은 정상이지만 경고 메시지가 성능 저하 유발

**원인 분석**
- `return-object` 속성 사용 시 `:item-title`에 문자열 전달
- Vuetify는 return-object 모드에서 함수 형태 기대
- 프레임워크의 타입 체크와 실제 사용 패턴 불일치

**해결 개념**
- Arrow function으로 속성 변환 로직 캡슐화
- `:item-title="(item) => item.속성명"` 패턴 사용
- Props에 함수 전달로 동적 속성 선택 가능

**핵심 학습 포인트**
- UI 라이브러리 props 타입 명세 숙지 중요
- return-object 옵션 사용 시 item-title/item-value는 함수여야 함
- 타입 안전성을 위한 프레임워크 제약 이해

**구글링 키워드**
- "Vuetify autocomplete return-object item-title"
- "v-autocomplete dynamic item title function"
- "Vuetify prop type validation error"
- "Vue component prop function vs string"

---

### 4. 잘못된 데이터베이스 컬럼 참조로 인한 수량 손실

**문제 상황**
- 저장된 생산통합 데이터의 모든 수량이 0으로 표시
- 조회는 정상 작동하지만 DB에 0이 저장되어 있음

**원인 분석**
- 영업계획 테이블에 `confirmed_prod_qty`(확정 수량)와 `system_prod_qty`(시스템 계산 수량) 두 컬럼 존재
- 저장 쿼리는 `confirmed_prod_qty`만 읽고 있었음
- 실제 데이터는 `system_prod_qty`에 있고 `confirmed_prod_qty`는 0
- 조회 쿼리는 `COALESCE(system_prod_qty, confirmed_prod_qty, 0)` 사용해서 문제 없었음

**해결 개념**
- 저장/조회 쿼리 간 컬럼 참조 일관성 확보
- COALESCE 함수로 우선순위 폴백(fallback) 구현
- 업무 로직에 맞는 컬럼 선택 (시스템 계산값 우선, 없으면 확정값)

**핵심 학습 포인트**
- 같은 도메인 데이터가 여러 컬럼에 분산 저장될 때 우선순위 명확히
- SQL COALESCE는 여러 컬럼 중 NULL 아닌 첫 값 반환
- 저장/조회 로직의 대칭성 검증 필요 (같은 데이터를 같은 방식으로)
- 컬럼명이 유사해도 업무 의미가 다를 수 있음 (confirmed vs system)

**구글링 키워드**
- "SQL COALESCE multiple columns priority"
- "database column fallback pattern"
- "confirmed vs system calculated values"
- "MySQL COALESCE vs IFNULL performance"
- "data warehouse fact table measure selection"

---

## 💻 새로 배운 기술

### Vue 3 Composition API
- **Map 기반 데이터 그룹핑**: 복합키 생성으로 다차원 그룹화
- **Reactive 상태 생명주기**: 탭 전환 시 명시적 정리 필요
- **Watch hook 활용**: activeTab 변경 감지해서 데이터 재로딩

### Vuetify 3
- **v-autocomplete 고급 사용**: return-object와 item-title 함수 조합
- **Props 타입 시스템**: 프레임워크 제약에 맞는 타입 전달
- **동적 컴포넌트 속성**: 탭 상태에 따른 조건부 렌더링

### SQL 쿼리 최적화
- **COALESCE 함수**: NULL 처리와 폴백 메커니즘
- **컬럼 선택 전략**: 업무 로직에 맞는 우선순위 설정
- **저장/조회 일관성**: 같은 데이터 흐름 보장

### 디버깅 기법
- **Console 로그 활용**: Proxy 객체 내부 구조 확인
- **MySQL 실행 계획**: DESCRIBE로 테이블 스키마 검증
- **데이터 추적**: 저장→DB→조회 전 구간 검증

---

## 🎯 오늘의 핵심 교훈

### 1. 데이터 고유성 판단 기준
단일 ID로 충분한지, 복합키가 필요한지는 **업무 요구사항**과 **DB 설계**에서 나온다. 프론트엔드는 DB의 UNIQUE 제약조건을 그대로 반영해야 함.

### 2. 상태 초기화는 명시적으로
반응형 프레임워크에서 "안 쓰는 데이터"도 명시적으로 비워야 함. Early return은 로직 단순화에 좋지만 상태 정리를 빠뜨리기 쉬움.

### 3. 프레임워크 타입 제약 존중
경고를 무시하고 "동작하면 OK"가 아니라, 프레임워크가 왜 그런 타입을 요구하는지 이해하고 따르기. 성능과 안정성에 영향.

### 4. 저장과 조회는 대칭적이어야
같은 데이터를 저장할 때와 읽을 때 다른 컬럼을 보면 안 됨. 조회 로직이 COALESCE 쓰면 저장도 COALESCE 써야 일관성 유지.

---

## 📚 추천 학습 자료

### 공식 문서
- [Vue 3 Reactivity Fundamentals](https://vuejs.org/guide/essentials/reactivity-fundamentals.html) - 반응형 데이터 관리
- [Vuetify v-autocomplete API](https://vuetifyjs.com/en/components/autocompletes/) - Props 상세 명세
- [MDN JavaScript Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) - Map 자료구조 활용
- [MySQL COALESCE Function](https://dev.mysql.com/doc/refman/8.0/en/comparison-operators.html#function_coalesce) - NULL 처리 패턴

### 검색 키워드
- "composite key pattern JavaScript frontend"
- "Vue reactive cleanup lifecycle"
- "Vuetify autocomplete advanced examples"
- "SQL column selection strategy business logic"



---

## 💡 회고

### 잘한 점
- 문제를 표면적으로 해결하지 않고 근본 원인까지 추적
- MySQL DESCRIBE로 테이블 구조 확인하며 가정 검증
- 여러 문제를 개별적으로 분리해서 순차적으로 해결

### 아쉬운 점
- 초기에 DB 스키마를 먼저 확인하지 않아 시행착오
- Vue 경고를 처음엔 무시하고 넘어가려 함
- 저장과 조회 쿼리를 동시에 비교 검토하지 않음

### 다음엔 이렇게
- 데이터 관련 이슈는 DB 스키마부터 확인
- 프레임워크 경고는 즉시 해결 (기술 부채 방지)
- 저장/조회/수정 로직은 세트로 검토
