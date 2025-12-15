# 2025-12-15 학습 노트

##  오늘의 작업 주제
긴급계획 데이터 저장 시 중복 데이터 문제 해결 및 긴급유무 판단 로직 구현

---

##  트러블슈팅

### 문제 1: 엑셀에서 변경한 값이 DB에 저장되지 않는 문제

**문제 상황:**
- 긴급계획 파일에서 수량을 변경했는데 (396→500, 134→1534, 408→500)
- DB에는 원본 값(396, 134, 408)이 저장됨
- 계획조회 화면에서 변경된 품목이 "긴급"이 아닌 "정규"로 표시됨

**원인 분석:**
1. **프론트엔드 병합 로직 문제**
   - 업로드 데이터(500)와 원본 데이터(396)를 병합할 때
   - 같은 품목+날짜 조합을 찾지 못해 두 개의 레코드가 모두 배열에 포함됨
   - 결과: `[{...값: 500}, {...값: 396}]` 형태로 중복 데이터 생성

2. **DB 저장 순서 문제**
   - 중복된 데이터가 순차적으로 저장됨
   - MySQL의 `ON DUPLICATE KEY UPDATE` 구문으로 인해
   - 나중에 저장된 값(396)이 먼저 저장된 값(500)을 덮어씀

**해결 개념:**
- **백엔드에서 중복 제거** 로직 추가
- 같은 품목+날짜+기간단위 조합을 키로 사용
- 딕셔너리를 사용해 중복 키는 첫 번째 값만 보존
- 프론트에서 업로드 데이터를 먼저 추가하므로 첫 번째 = 변경된 값

**핵심 학습 포인트:**
- 배열 중복 제거는 딕셔너리/해시맵 사용이 효율적
- `ON DUPLICATE KEY UPDATE`는 마지막 실행 값으로 덮어씀
- 데이터 병합 시 고유 키 설계가 중요함

**구글링 키워드:**
- "Python dictionary deduplication by key"
- "MySQL ON DUPLICATE KEY UPDATE behavior"
- "Array deduplication using hash map"

---

### 문제 2: 긴급계획 조회 시 모든 품목이 "정규"로 표시

**문제 상황:**
- 긴급계획에서 변경된 품목도 "정규"로 표시됨
- 긴급유무 판단 로직이 작동하지 않음

**원인 분석:**
- 백엔드 조회 쿼리에서 이전 버전 데이터와 LEFT JOIN
- 수량 비교로 변경 여부를 판단
- 하지만 DB에 저장된 값이 동일해서 변경 감지 실패

**해결 개념:**
- 중복 제거 로직 수정 후 정상 작동 예상
- LEFT JOIN으로 이전 버전과 현재 버전 비교
- 품목별로 수량 차이가 있으면 changed_items 집합에 추가
- 긴급계획이면서 changed_items에 있으면 "긴급", 아니면 "정규"

**핵심 학습 포인트:**
- SQL LEFT JOIN으로 이전 버전 데이터 비교 가능
- Python set 자료구조로 변경 품목 관리
- 조회 시점에 동적으로 긴급유무 판단 (컬럼 추가 없이)

**구글링 키워드:**
- "SQL LEFT JOIN self join previous version"
- "Python set for tracking changes"
- "Dynamic field calculation in SQL query"

---

##  새로 배운 기술

### 1. 데이터 중복 제거 패턴

**개념:**
- 배열에서 특정 조건으로 중복 제거 시 딕셔너리 활용
- 복합 키(품목+날짜+단위)를 문자열로 결합하여 사용
- 딕셔너리는 키 중복을 자동으로 방지

**적용 방법:**
```text
1. 빈 딕셔너리 생성
2. 각 레코드마다 고유 키 생성 (예: "품목|날짜|단위")
3. 키가 없으면 추가, 있으면 무시 (또는 업데이트)
4. 딕셔너리 값들을 리스트로 변환
```

**더 공부할 것:**
- 대용량 데이터에서의 성능 비교 (딕셔너리 vs pandas drop_duplicates)
- 메모리 효율적인 중복 제거 방법

---

### 2. MySQL ON DUPLICATE KEY UPDATE 동작 방식

**개념:**
- INSERT 시 UNIQUE 키 충돌 시 UPDATE 실행
- 같은 키로 여러 번 실행하면 마지막 값이 적용됨
- 중복 데이터 배치 저장 시 주의 필요

**함정:**
- 순서가 중요함 (먼저 저장 → 나중 저장이 덮어씀)
- 중복 제거 없이 사용하면 의도치 않은 결과 발생

**더 공부할 것:**
- UPSERT 패턴의 다른 데이터베이스 구현 방식
- PostgreSQL의 ON CONFLICT DO UPDATE
- 트랜잭션 내에서의 동작 방식

---

### 3. 프론트엔드 디버깅 로깅 전략

**개념:**
- 데이터 흐름의 각 단계마다 로그 추가
- 업로드 → 병합 → 저장 전 각 단계의 데이터 상태 확인
- 샘플 데이터로 특정 케이스만 추적

**적용 방법:**
```text
1. 업로드 직후: 파싱된 원본 데이터
2. 병합 후: 원본+업로드 합쳐진 데이터
3. 저장 직전: 백엔드로 보낼 최종 데이터
4. 백엔드 수신: 실제 받은 데이터
5. 중복 제거 후: 최종 저장될 데이터
```

**핵심:**
- 전체 데이터 로깅은 성능 저하 → 샘플만 로깅
- 특정 품목(88700BC210CRT 등)만 추적
- 각 단계의 데이터 개수 확인으로 이상 감지

**더 공부할 것:**
- 프로덕션 환경에서의 로깅 레벨 관리
- 로그 수집 및 분석 도구 (ELK Stack)

---

##  오늘의 핵심 교훈

### 1. 데이터 정합성 검증의 중요성
- 프론트 → 백엔드 → DB로 데이터가 전달될 때 각 단계 검증 필요
- 중간 단계에서 데이터 변형/중복 발생 가능성 항상 고려

### 2. 디버깅은 가정을 배제하고 실제 데이터 확인
- "병합이 잘 되었을 것이다" → 실제 로그로 중복 발견
- 각 단계마다 실제 값을 출력해서 확인

### 3. DB 제약조건 활용 시 동작 방식 이해 필수
- ON DUPLICATE KEY UPDATE의 순서 의존성
- 의도와 다른 결과 방지를 위한 사전 중복 제거

### 4. 프론트엔드 배열 병합 로직의 함정
- findIndex로 찾을 때 조건이 너무 엄격하면 찾지 못함
- 같은 데이터가 중복 추가될 수 있음
- 백엔드에서 방어 로직 추가가 안전

---

##  추천 학습 자료

### 공식 문서
- [MySQL - INSERT ON DUPLICATE KEY UPDATE](https://dev.mysql.com/doc/refman/8.0/en/insert-on-duplicate.html)
- [Python - Dictionary Methods](https://docs.python.org/3/tutorial/datastructures.html#dictionaries)
- [Vue.js - Debugging Guide](https://vuejs.org/guide/essentials/reactivity-fundamentals.html)

### 검색 키워드
- "prevent duplicate data before database insert"
- "Python deduplication techniques performance"
- "MySQL UPSERT best practices"
- "frontend backend data validation pattern"
- "debugging data flow in full stack application"

### 다음에 공부할 것
1. **대용량 데이터 중복 제거 최적화**
   - pandas vs 딕셔너리 성능 비교
   - 메모리 효율적인 스트리밍 처리

2. **데이터 무결성 검증 패턴**
   - 프론트-백엔드 간 데이터 체크섬
   - 저장 전후 검증 로직

3. **SQL 조인 성능 최적화**
   - LEFT JOIN vs INNER JOIN 성능 비교
   - 인덱스 활용 전략

---

##  관련 기술 스택

- **Frontend:** Vue.js 3 (Composition API), JavaScript
- **Backend:** Python, Flask
- **Database:** MySQL
- **Debugging:** Browser DevTools, Python logging
- **Data Processing:** Array/List manipulation, Dictionary


---

