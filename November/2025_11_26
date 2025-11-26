# 2025-11-26 개발 학습 노트

## 📚 오늘 배운 기술 스택
- Flask-SQLAlchemy (ORM)
- ReportLab (PDF 생성)
- Matplotlib (차트/그래프)
- Python Backend 최적화

---

## 🔥 트러블슈팅 1: 불량 분석 페이지 데이터 0 출력 문제

### 문제 상황
- 불량분석 페이지에서 총 불량수, 불량유형수가 0으로 표시됨
- "전체 불량 유형별 분포"에 데이터 없음
- 하지만 "공정별 일별 불량추이"는 정상 출력

### 원인 분석
**SQLAlchemy Relationship의 lazy loading 설정 문제**
- 이전 성능 최적화 작업에서 `lazy='noload'` 설정
- Relationship을 통해 데이터 접근 시 자동으로 로드되지 않음
- 결과: 연관 테이블 데이터가 비어있는 상태로 반환

### 해결 개념
**직접 JOIN 쿼리로 변경**
```
기존: relationship을 통한 암묵적 로딩
변경: db.session.query().join()으로 명시적 JOIN
```

### 핵심 학습 포인트
- **N+1 쿼리 문제**: Relationship 사용 시 발생하는 성능 문제
- **Lazy Loading 전략**: select, joined, subquery, noload 등의 차이
- **명시적 JOIN**: 성능과 명확성을 위해 직접 JOIN 쿼리 작성

### 참고 키워드
- SQLAlchemy lazy loading strategies
- SQLAlchemy JOIN query
- N+1 query problem
- ORM performance optimization

---

## 🔥 트러블슈팅 2: 라인별 성능 분석에서 라인 누락 문제

### 문제 상황
- 총 12개 라인인데 보고서에 일부 라인만 표시됨
- 처음엔 4,5번 라인 빠짐
- 수정 후 14,15번 라인 빠짐

### 원인 분석
**Python 리스트 슬라이싱 제한**
- 코드에 `lines[:10]` 형태로 상위 10개만 추출
- 의도하지 않은 데이터 제한

### 해결 개념
```
잘못된 접근: 임의로 데이터 개수 제한
올바른 접근: 모든 데이터 표시 후 필요시 페이징 처리
```

### 추가 수정 사항
- "불량 TOP 5" → "불량 TOP 10"으로 확장
- 불량 유형도 TOP 5 → TOP 10으로 확장

### 핵심 학습 포인트
- **데이터 제한의 명시성**: 코드에 숨겨진 제한 조건 주의
- **사용자 요구사항 반영**: 데이터 개수는 비즈니스 요구사항에 따름
- **디버깅 로그 활용**: print문으로 처리되는 데이터 확인

### 참고 키워드
- Python list slicing
- Data pagination
- SQL LIMIT vs Python slicing

---

## 🔥 트러블슈팅 3: PDF 보고서에 차트가 표시되지 않음

### 문제 상황
- 차트 생성 함수는 만들었지만 PDF에 표시 안 됨
- 프론트엔드에 `includeCharts` 옵션 체크박스가 없었음

### 원인 분석
**프론트엔드와 백엔드 옵션 불일치**
- 백엔드는 `includeCharts` 옵션 확인
- 프론트엔드는 해당 옵션 제공 안 함
- 결과: 항상 False로 처리되어 차트 미생성

### 해결 개념
```
임시 해결: 조건문 제거하고 항상 차트 생성
완전한 해결: 프론트엔드에 옵션 추가 또는 다른 옵션과 연동
```

### 핵심 학습 포인트
- **프론트엔드-백엔드 계약**: API 옵션은 양쪽이 동의해야 함
- **기본값 설정**: 옵션이 없을 때의 기본 동작 정의
- **단계별 테스트**: 함수 생성 후 호출 여부 확인

### 참고 키워드
- Frontend-Backend contract
- API request validation
- Default parameter handling

---

## 📊 새로운 기능 구현: PDF 보고서 차트 추가

### 구현 내용
3가지 차트를 PDF에 통합:
1. 공정별 생산량 바 차트
2. 라인별 OEE/불량률 차트
3. 불량 유형 파이 차트

### 사용 기술
**Matplotlib + ReportLab 통합**
```
1. Matplotlib로 차트 생성
2. PNG 파일로 저장
3. ReportLab Image 객체로 PDF에 삽입
```

### 핵심 학습 포인트
- **Matplotlib backend 설정**: `matplotlib.use('Agg')` GUI 없이 실행
- **한글 폰트 설정**: NanumGothic.ttf 경로 지정
- **이미지 크기 조정**: inch 단위로 PDF에 맞게 조정
- **파일 정리**: 생성된 PNG 파일 관리

### 주의사항
- 서버 환경에서 matplotlib는 Agg 백엔드 필수
- 한글 폰트가 없으면 깨짐 발생
- 차트 생성 후 plt.close()로 메모리 해제

### 참고 키워드
- Matplotlib Agg backend
- Matplotlib Korean font
- ReportLab Image insertion
- Python temporary files

---

## 🎨 PDF 보고서 디자인: 회사 서류 형식으로 개선

### 기존 문제점
- 색상이 알록달록해서 비전문적
- 테이블 마진/패딩이 불규칙
- 회사 서류 필수 요소 부족

### 개선 사항

#### 1. 색상 통일
```
개념: 기업 컬러 시스템
- 메인 컬러 1개 선택 (네이비 블루)
- 보조 컬러 2개 (흰색, 연한 회색)
- 모든 요소에 일관되게 적용
```

#### 2. 레이아웃 표준화
```
개념: 디자인 시스템
- 마진: 0.75인치로 고정
- 패딩: 상하 12px, 좌우 10px 통일
- 간격: 섹션 간 0.3인치 일정
```

#### 3. 회사 서류 필수 요소 추가
- 문서 헤더: 문서 분류, 번호, 보안 등급, 작성 일자
- 제목 및 구분선
- 섹션 헤더 (통일된 스타일)
- 결재란: 작성자/검토자/승인자
- 페이지 번호 (하단 중앙)
- 기밀 표시 푸터

### ReportLab 핵심 개념

#### TableStyle 속성
```
BACKGROUND: 배경색
TEXTCOLOR: 글자색
ALIGN/VALIGN: 정렬
FONTNAME/FONTSIZE: 폰트
GRID: 테두리
TOPPADDING/BOTTOMPADDING: 여백
ROWBACKGROUNDS: 행 교차 색상
```

#### 페이지 제어
```
PageBreak: 페이지 나누기
Spacer: 빈 공간
onFirstPage/onLaterPages: 페이지별 콜백
```

#### 커스텀 함수
```
canvas.drawCentredString(): 중앙 정렬 텍스트
canvas.getPageNumber(): 현재 페이지 번호
```

### 핵심 학습 포인트
- **일관성**: 디자인 시스템의 가장 중요한 원칙
- **가독성**: 흰 공간(White space) 활용
- **전문성**: 회사 서류의 필수 구성 요소
- **유지보수**: 변수로 색상/크기 관리

### 참고 키워드
- ReportLab TableStyle
- PDF page layout design
- Corporate document design
- Design system principles
- ReportLab canvas drawing

---

## 🛠️ 기술 스택별 학습 정리

### Flask-SQLAlchemy
**배운 것:**
- Relationship lazy loading 전략
- JOIN 쿼리 작성법
- N+1 쿼리 문제 해결

**더 공부할 것:**
- SQLAlchemy Session 관리
- Query 성능 프로파일링
- Database 인덱스 최적화

### ReportLab
**배운 것:**
- PDF 문서 구조 (SimpleDocTemplate)
- Table 스타일링
- Image 삽입
- 페이지 콜백 함수

**더 공부할 것:**
- 복잡한 레이아웃 (Frames, Templates)
- 동적 콘텐츠 생성
- PDF 성능 최적화

### Matplotlib
**배운 것:**
- Agg 백엔드 사용
- 한글 폰트 설정
- 차트를 파일로 저장
- 메모리 관리 (plt.close)

**더 공부할 것:**
- 다양한 차트 타입
- 스타일 커스터마이징
- 인터랙티브 차트

---

## 💡 오늘의 핵심 교훈

### 1. 성능 최적화는 양날의 검
- `lazy='noload'` 같은 최적화는 부작용 고려 필요
- 최적화 전 성능 측정 필수
- 명시적 코드가 암묵적 코드보다 낫다

### 2. 프론트엔드-백엔드 동기화
- API 계약은 양쪽이 합의해야 함
- 옵션 추가 시 전체 플로우 확인
- 기본값 설정 중요

### 3. 디자인 시스템의 중요성
- 색상, 간격, 패딩 등을 변수로 관리
- 일관성이 전문성을 만든다
- 코드 재사용성 향상

### 4. 문서화와 주석
- 왜 이렇게 했는지 기록
- 제한 조건은 명시적으로
- 나중의 나를 위한 배려

---

## 📖 추천 학습 자료

### 공식 문서
- [SQLAlchemy 공식 문서](https://docs.sqlalchemy.org/)
- [ReportLab 공식 가이드](https://www.reportlab.com/docs/reportlab-userguide.pdf)
- [Matplotlib 공식 문서](https://matplotlib.org/stable/contents.html)

### 검색할 키워드
- "SQLAlchemy lazy loading best practices"
- "ReportLab table styling examples"
- "Matplotlib Korean font Linux"
- "PDF generation with Python"
- "N+1 query problem solutions"

### 관련 개념
- ORM (Object-Relational Mapping)
- Database Query Optimization
- PDF Document Structure
- Data Visualization
- Design Systems

---

## 🎯 다음에 할 일

### 배포 관련
- [x] analysis.py 수정 완료
- [x] report.py 수정 완료
- [ ] 서버에 파일 업로드 (WinSCP)
- [ ] matplotlib 설치 확인
- [ ] 서버에서 보고서 생성 테스트

### 추가 학습
- [ ] SQLAlchemy Query 성능 측정 방법
- [ ] ReportLab 고급 레이아웃
- [ ] Matplotlib 스타일 커스터마이징
- [ ] Python 비동기 처리 (대용량 PDF)

---

### 주의사항
- 서버 환경에서 matplotlib Agg 백엔드 필수
- 한글 폰트 경로 확인
- PDF 생성 후 임시 PNG 파일 정리
- 메모리 사용량 모니터링 (대량 보고서 생성 시)

---

**작성일**: 2025-11-26
**작성자**: 개발 학습 노트
**태그**: #Flask #SQLAlchemy #ReportLab #Matplotlib #PDF #트러블슈팅
