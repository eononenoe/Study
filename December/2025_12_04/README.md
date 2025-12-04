# 2025-12-04 학습 노트: Excel LOT 컬럼 처리 및 Custom Number Format 복원

## 📌 작업 개요
SMT 생산 데이터 업로드 시 LOT NO 컬럼의 다양한 표기 방식 인식 및 Custom Number Format으로 인한 데이터 손실 문제 해결

---

## 🔥 트러블슈팅

### 문제 1: LOT 컬럼명 인식 실패
**문제 상황**
- LOT NO 컬럼이 엑셀에서 "Lot /No", "Lot\n/No", "Lot No" 등 다양한 형태로 존재
- 고정된 컬럼명 매칭으로는 인식 실패

**원인 분석**
- 엑셀에서 셀 병합, 줄바꿈, 특수문자 포함된 헤더 사용
- pandas가 읽을 때 "\n" 같은 제어 문자가 포함됨

**해결 개념**
- 정규식 패턴 매칭 사용: `^lot.*no$` (대소문자 무관, DOTALL 플래그)
- 컬럼명 정규화: 줄바꿈 제거, 공백 정리 후 매칭
- 유연한 패턴 인식으로 다양한 표기 방식 대응

**핵심 학습 포인트**
- 정규식의 IGNORECASE, DOTALL 플래그 활용
- pandas 컬럼명 정규화 방법 (str.replace, str.strip)
- 실무 데이터는 항상 일관성 없음 → 유연한 처리 필수

**구글링 키워드**
- "pandas read excel column name normalization"
- "python regex DOTALL flag"
- "flexible column matching pandas"

---

### 문제 2: LOT NO 데이터 손실 (Custom Number Format)
**문제 상황**
- 엑셀 화면: "5K31", "5L05"
- DB 저장: "31", "5" (prefix 사라짐)
- 일부는 올바르게 저장되지만 일부는 숫자만 저장됨

**원인 분석**
- 엑셀 Custom Number Format 사용
  - 실제 셀 값: `31` (숫자)
  - 표시 형식: `"5K"00` (prefix 추가)
  - 화면 표시: `5K31`
- pandas는 **실제 값**만 읽고 **표시 형식 무시**
- 결과: 31만 저장됨

**해결 개념**
1. **LOT NO 규칙 파악**
   - 형식: 년도(1자리) + 월(알파벳) + 일(2자리)
   - 예: 5K31 = 2025년 10월 31일
   - 월 매핑: A=1월, B=2월, ..., J=9월, K=10월, L=11월, M=12월 **(I 제외)**

2. **알고리즘: 점프 감지 방식**
   - 엑셀을 위→아래로 읽으면서 숫자 변화 감지
   - 큰 숫자(20~31) → 작은 숫자(1~10)로 점프 시 월 변경 인식
   - 예: `23 → 1` 감지 시 → 10월(K) → 11월(L) 변경

3. **prod_date 활용**
   - 생산일자(prod_date)에서 년도, 기준 월 추출
   - 점프 감지와 조합하여 정확한 월 판단

4. **완전한 LOT NO 처리**
   - 이미 "5L05" 형태로 저장된 데이터는 그대로 유지
   - 현재 월 정보 업데이트용으로 활용

**핵심 학습 포인트**
- Excel Custom Number Format은 표시 전용, 실제 값과 다름
- pandas는 실제 값만 읽음 (표시 형식 무시)
- openpyxl로 number_format 속성 접근 가능하지만 복잡
- **비즈니스 로직으로 복원**: 패턴 분석 + 날짜 정보 조합이 더 효율적
- 순차 데이터 처리 시 상태 관리 (current_month, previous_day)
- 엣지 케이스 처리: 12월→1월 넘어갈 때 년도 증가

**구글링 키워드**
- "pandas read excel custom number format"
- "openpyxl number_format property"
- "excel display format vs value"
- "python sequential data pattern detection"

---

## 🛠 새로 배운 기술

### 1. 정규식 고급 활용
- **re.DOTALL**: `.`이 줄바꿈(\n)도 매칭
- **re.IGNORECASE**: 대소문자 무관
- 실무 패턴: `^lot.*no$`로 다양한 변형 모두 인식

### 2. pandas DataFrame 순차 처리
- `df.iterrows()`로 행 단위 순회
- 이전 행 정보 저장하여 패턴 감지 (previous_day)
- 상태 관리 (current_month, current_year) 필요

### 3. Excel 데이터 처리 best practices
- 헤더 위치 동적 탐색 (row 0~14 시도)
- 컬럼명 정규화 후 매칭
- Custom Format 문제 인지 및 대응

### 4. 알고리즘 설계
- 점프 감지 알고리즘: `previous > current + threshold`
- 상태 머신 패턴: 순차 데이터에서 월 전환 감지
- Fallback 전략: 실패 시 원본 값 사용

---

## 💡 오늘의 핵심 교훈

### 1. 실무 데이터는 예상과 다르다
- 항상 다양한 표기 방식, 오류, 예외 존재
- 유연한 처리 로직 필수
- 정규식, 패턴 매칭이 고정 매칭보다 강력

### 2. Excel ≠ 데이터베이스
- 표시 형식 vs 실제 값 차이 인지
- pandas는 실제 값만 읽음
- 복잡한 형식은 비즈니스 로직으로 복원

### 3. 알고리즘 설계의 중요성
- 단순 날짜 매핑이 아닌 패턴 분석 필요
- 순차 데이터에서 "점프" 감지로 상태 전환 인식
- 이전 값 저장 및 비교가 핵심

### 4. 문제 해결 접근법
1. 문제 원인 정확히 파악 (Custom Format)
2. 비즈니스 규칙 이해 (LOT NO 규칙)
3. 알고리즘 설계 (점프 감지)
4. 엣지 케이스 처리 (년도 넘어감, 빈 값)

---

## 📚 추천 학습 자료

### 공식 문서
- [pandas.read_excel 문서](https://pandas.pydata.org/docs/reference/api/pandas.read_excel.html)
- [Python re 모듈 문서](https://docs.python.org/3/library/re.html)
- [openpyxl number_format](https://openpyxl.readthedocs.io/en/stable/api/openpyxl.styles.numbers.html)

### 검색 키워드
- "excel custom number format python read"
- "pandas sequential data state machine"
- "python regex multiline pattern matching"
- "excel display value vs actual value"
- "pandas column name flexible matching"

### 관련 주제
- Excel의 다양한 데이터 타입 처리
- 순차 데이터에서 패턴 감지 (시계열 분석 기초)
- 상태 머신 패턴 (State Machine Pattern)
- 데이터 정규화 및 클렌징

---


## 🎯 기억할 것

**"실무 데이터는 항상 지저분하다. 유연하게 대응하라."**

- 고정된 로직 < 패턴 인식 로직
- 표시 값 ≠ 실제 값 (Excel)
- 비즈니스 규칙 이해가 기술보다 중요
- 순차 처리 시 상태 관리 필수
