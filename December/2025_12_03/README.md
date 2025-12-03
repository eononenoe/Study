# 2024-12-03 학습 노트 - DB 설계 분석 및 화면-테이블 매핑

## 📋 작업 개요
PowerPoint 화면설계 문서(65~77페이지)와 실제 데이터베이스 테이블 구조를 비교 분석하여 누락된 필드를 파악하고, 수정 SQL을 작성하는 작업을 진행했습니다.

---

## 🔍 트러블슈팅

### 1. 화면 필드와 DB 컬럼 불일치 문제

**문제 상황:**
- PowerPoint 화면설계에 표시된 필드들이 실제 데이터베이스 테이블에 없는 경우 발견
- 특히 부서 테이블에 "부서코드" 필드가 없어서 사용자가 직접 코드를 입력할 수 없는 치명적 문제 발견

**원인 분석:**
- 화면설계와 DB 설계가 별도로 진행되면서 동기화 문제 발생
- AUTO_INCREMENT ID만 있고 사용자 입력용 코드 컬럼이 누락됨
- "비고", "메모" 같은 보조 필드들이 체계적으로 누락됨

**해결 개념:**
- 화면 테이블 구조를 체계적으로 추출하는 Python-pptx 라이브러리 활용
- 각 페이지별로 테이블 헤더와 데이터를 추출하여 실제 필요한 필드 파악
- SQL 테이블 구조와 1:1 매핑하여 누락 필드 식별

**핵심 학습:**
- 화면설계서의 "표시되는 필드"가 곧 DB 컬럼 요구사항
- JOIN으로 해결 가능한 것과 실제 컬럼 추가가 필요한 것을 구분해야 함
- 사용자 입력 필드 vs 시스템 자동생성 필드를 명확히 구분 필요

---

### 2. 대분류-중분류 계층 구조 이해

**문제 상황:**
- 처음에는 "기준코드" 화면에 "중분류 코드", "설명", "비 고" 3개 필드만 있다고 파악
- 실제로는 대분류-중분류 계층 구조로 5개 필드가 필요했음

**원인 분석:**
- PPT에 2개의 테이블이 분리되어 있었는데, 이를 개별 테이블로 착각
- 실제로는 하나의 화면에서 대분류와 중분류를 함께 표시하는 구조

**해결 개념:**
- 자기참조(Self-Referencing) 테이블 구조 이해
- `fk_parent_code_id`를 통해 상위 코드를 참조하는 구조
- LEFT JOIN으로 부모-자식 관계를 조회하는 패턴

**SQL 조회 패턴:**
```sql
SELECT
    parent.code_name AS 대분류코드,
    parent.code_class_detail AS 대분류설명,
    child.code_name AS 중분류코드,
    child.code_class_detail AS 중분류설명
FROM base_code child
LEFT JOIN base_code parent ON child.fk_parent_code_id = parent.id
```

**핵심 학습:**
- 계층형 데이터는 자기참조 FK로 구현
- 화면에 표시되는 모든 필드를 정확히 세어야 함
- 테이블 2개 = 2개 테이블이 아닐 수 있음 (화면 구성 vs DB 구조)

---

### 3. 비트플래그 vs 별도 컬럼 설계

**문제 상황:**
- 권한 관리에서 "보기", "신규", "수정", "삭제", "인쇄", "엑셀" 6개 권한이 있음
- 기존 설계는 `menu_auth` INT 하나로 비트플래그 방식 사용
- 화면에서는 6개 체크박스로 개별 표시되어야 함

**원인 분석:**
- 저장 공간 절약을 위해 비트플래그 사용
- 하지만 실제 개발에서는 쿼리 복잡도가 높아지고 유지보수가 어려움

**해결 개념:**
- 각 권한을 TINYINT(1) 별도 컬럼으로 분리 권장
- 기존 menu_auth는 호환성을 위해 유지
- 신규 개발은 auth_view, auth_create 등 명시적 컬럼 사용

**비트플래그 패턴:**
```
menu_auth = 63 (0b111111)
  1비트: 보기
  2비트: 신규
  4비트: 수정
  8비트: 삭제
  16비트: 인쇄
  32비트: 엑셀
```

**핵심 학습:**
- 비트플래그는 저장공간은 절약되지만 가독성과 유지보수성이 떨어짐
- 권한 같은 비즈니스 로직은 명시적 컬럼이 더 나을 수 있음
- 마이그레이션 시 기존 데이터 변환 쿼리 필요

---

## 🛠️ 새로 배운 기술

### 1. Python-pptx 라이브러리

**사용 목적:**
- PowerPoint 파일에서 텍스트와 테이블 구조 추출
- 화면설계서를 프로그래밍 방식으로 분석

**배운 내용:**
- `Presentation` 객체로 PPT 파일 로드
- `slide.shapes`로 모든 도형 순회
- `MSO_SHAPE_TYPE.TABLE`로 테이블만 필터링
- `table.rows`와 `cell.text`로 데이터 추출
- `notes_slide.notes_text_frame.text`로 슬라이드 노트 읽기

**활용 가능성:**
- 화면설계서 자동 분석
- 문서 기반 테스트 케이스 생성
- 설계서 변경 이력 추적

**더 공부할 것:**
- 표 스타일 및 서식 정보 추출
- 차트, 이미지 등 다른 객체 처리
- 대용량 PPT 파일 성능 최적화

---

### 2. SQL ALTER TABLE 작성 패턴

**배운 내용:**
- 여러 컬럼을 한 번에 추가하는 방법 (쉼표로 구분)
- COMMENT를 통한 컬럼 설명 추가 중요성
- DEFAULT와 ON UPDATE CURRENT_TIMESTAMP 활용
- UNIQUE 제약 조건 추가 방법

**좋은 패턴:**
```sql
ALTER TABLE table_name
  ADD COLUMN col1 VARCHAR(20) UNIQUE COMMENT '설명1',
  ADD COLUMN col2 VARCHAR(512) COMMENT '설명2';
```

**주의사항:**
- 기존 데이터가 있을 때 NOT NULL 추가 시 DEFAULT 필요
- UNIQUE 추가 시 기존 중복 데이터 정리 선행 필요
- 트랜잭션 단위로 실행하여 롤백 가능하도록

**더 공부할 것:**
- ALTER TABLE 성능 최적화 (Online DDL)
- 대용량 테이블 스키마 변경 전략
- 무중단 마이그레이션 기법

---

### 3. 기준정보(Master Data) 관리 개념

**배운 내용:**
- 기준정보는 시스템 운영의 기반이 되는 핵심 데이터
- 코드 테이블, 권한 테이블, 채번 규칙 등이 포함됨
- 화면 구성과 직접 연결되어 있어 누락 시 화면 동작 불가

**우선순위 분류:**
- 최우선: 코드, 메뉴, 채번규칙 (시스템 초기화 필수)
- 높음: 권한, 부서, 공정 (기본 업무 수행 필요)
- 중간: 거래처, 차종 (업무 데이터 입력 후 필요)

**데이터 제공 형식:**
- 엑셀: 컬럼명이 SQL과 일치해야 함
- CSV: UTF-8 인코딩 필수
- SQL INSERT: 바로 실행 가능한 형태

**더 공부할 것:**
- 마스터 데이터 버전 관리
- 환경별 기준정보 동기화 (개발/스테이징/운영)
- 기준정보 변경 이력 관리

---

## 💡 오늘의 핵심 교훈

### 1. 설계서는 여러 번 검증해야 한다
- 한 번 읽고 파악한 것은 불완전할 수 있음
- 실제 데이터와 비교하며 재검증 필요
- 예: "3개 필드"라고 생각했는데 실제로는 "5개 필드"

### 2. 화면 = DB 요구사항
- 화면에 표시되는 모든 필드는 어딘가에서 가져와야 함
- JOIN으로 해결할지, 컬럼 추가할지 설계 단계에서 결정
- 사용자 입력 필드는 반드시 별도 컬럼 필요

### 3. 문서화의 중요성
- README.md로 전체 분석 정리
- SQL_실행_가이드.md로 실행 방법 문서화
- 기준정보_RawData_요청목록.md로 요청사항 명확화
- 나중에 다시 봐도 이해할 수 있도록

### 4. 우선순위를 명확히
- 심각한 문제 (부서코드 없음) vs 경미한 문제 (비고 없음)
- 최우선/높음/중간/참고용으로 분류
- 한정된 시간에 중요한 것부터 처리

### 5. 커뮤니케이션 준비
- 대표님께 요청할 때 체계적인 목록 필요
- "데이터 주세요"가 아니라 "이런 형식으로 이런 데이터 필요합니다"
- 입수 날짜, 확인 여부, 우선순위 표시

---

## 📚 추천 학습 자료

### SQL 관련
- **검색 키워드:**
  - "ALTER TABLE best practices MySQL"
  - "self-referencing foreign key design pattern"
  - "database normalization vs denormalization"
  - "master data management strategies"

- **공식 문서:**
  - MySQL ALTER TABLE 문법: https://dev.mysql.com/doc/refman/8.0/en/alter-table.html
  - Foreign Key 제약: https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html

### Python 관련
- **검색 키워드:**
  - "python-pptx extract tables tutorial"
  - "python-pptx read notes"
  - "python automate powerpoint analysis"

- **공식 문서:**
  - python-pptx: https://python-pptx.readthedocs.io/

### DB 설계
- **검색 키워드:**
  - "UI to database mapping"
  - "screen design to ERD mapping"
  - "hierarchical data in relational database"
  - "code table design patterns"

---

## 📊 작업 통계

- **분석한 화면:** 13개 페이지 (65~77)
- **확인한 테이블:** 6개 (woobo_gen_ddl_42.sql) + 1개 (setup_ddl.sql)
- **발견한 누락 필드:** 15개
  - 심각: 3개 (부서코드, 변경일자, 후미자+거래처코드)
  - 중간: 4개
  - 경미: 8개
- **작성한 문서:** 4개
  - README.md (전체 분석)
  - ALTER_ALL.sql (수정 SQL)
  - SQL_실행_가이드.md (실행 가이드)
  - 기준정보_RawData_요청목록.md (요청 목록)

---

## 🎯 핵심 성과

1. **체계적 분석:** 화면설계와 DB를 1:1 매핑하여 누락 필드 완벽 파악
2. **실행 가능한 산출물:** 바로 실행 가능한 ALTER SQL 작성
3. **명확한 커뮤니케이션:** 우선순위가 명시된 요청 목록 작성
4. **재사용 가능한 프로세스:** PPT 분석 방법론 확립

---
