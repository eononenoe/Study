# 2025-12-14 학습 노트 - React Native 반응형 디자인

## 1. 트러블슈팅

### 문제 1: 온보딩 화면 페이지 전환이 작동하지 않음
**문제 상황**
- FlatList에서 "다음" 버튼을 눌러도 다음 슬라이드로 넘어가지 않음
- 스크롤해도 진행 표시 점(dot)이 실시간으로 업데이트되지 않음

**원인 분석**
- `scrollToIndex` 메서드가 React Native에서 불안정하게 작동
- 인덱스 기반 스크롤이 아닌 오프셋 기반 스크롤이 더 안정적

**해결 개념**
- `scrollToIndex` 대신 `scrollToOffset` 사용
- 화면 너비를 기준으로 정확한 픽셀 오프셋 계산
- `getItemLayout` 프롭으로 각 아이템의 정확한 위치 명시
- 스크롤 이벤트 핸들러로 현재 인덱스 실시간 계산

**핵심 학습 포인트**
- FlatList의 `scrollToIndex`는 예측 불가능한 동작을 할 수 있음
- `scrollToOffset`과 `getItemLayout`을 함께 사용하면 정확한 제어 가능
- 수동으로 스크롤 위치를 추적하는 것이 더 안정적

**구글링 키워드**
- "React Native FlatList scrollToIndex not working"
- "React Native FlatList scrollToOffset vs scrollToIndex"
- "React Native FlatList getItemLayout"

---

### 문제 2: 다양한 화면 크기에서 레이아웃이 깨짐
**문제 상황**
- iPhone SE: 버튼이 화면 밖으로 벗어남
- iPad: 3개의 슬라이드가 동시에 보임
- 중간 크기 기기: 콘텐츠가 세로 중앙에 위치하지 않음

**원인 분석**
- 고정된 픽셀 값 사용으로 다양한 화면 크기에 대응 불가
- FlatList가 화면 전체 너비를 사용하여 큰 화면에서 여러 슬라이드 표시
- Flex 레이아웃만으로는 세밀한 제어 부족

**해결 개념**
- 화면 크기별 최대 너비 제한 (MAX_WIDTH)
- 슬라이드 컨테이너를 화면 중앙에 배치
- 화면 높이에 따른 동적 여백 계산 (5단계 분기)
- `snapToInterval`로 정확한 스냅 동작 구현

**핵심 학습 포인트**
- 반응형 디자인에서 최대/최소 크기 제한이 중요
- Flexbox만으로는 모든 화면 크기 대응이 어려움
- 화면 크기에 따른 조건부 스타일링 필요

**구글링 키워드**
- "React Native responsive design best practices"
- "React Native FlatList pagingEnabled multiple screens"
- "React Native screen size conditional styling"

---

### 문제 3: 옆 슬라이드가 살짝 보이는 현상
**문제 상황**
- 현재 슬라이드 옆에 다음/이전 슬라이드의 일부가 보임
- 시각적으로 깔끔하지 않고 사용자 혼란 유발

**원인 분석**
- 컨테이너의 `overflow` 속성이 설정되지 않음
- FlatList의 스냅 정렬이 정확하지 않음

**해결 개념**
- 컨테이너와 래퍼에 `overflow: 'hidden'` 적용
- `snapToAlignment`를 `"start"`로 설정
- `removeClippedSubviews={true}` 옵션으로 화면 밖 뷰 제거

**핵심 학습 포인트**
- CSS의 `overflow: hidden`이 React Native에서도 동일하게 작동
- FlatList 성능 최적화를 위한 `removeClippedSubviews` 옵션
- 스냅 정렬 옵션에 따라 동작이 크게 달라짐

**구글링 키워드**
- "React Native FlatList hide overflow"
- "React Native snapToInterval not working properly"
- "React Native removeClippedSubviews"

---

## 2. 새로 배운 기술

### react-native-size-matters (★★★★★)
**배운 것**
- React Native 반응형 디자인의 업계 표준 라이브러리
- 기준 화면(보통 375x812)을 설정하고 자동으로 모든 기기에 스케일링
- ScaledSheet API로 특수 어노테이션 사용 가능

**핵심 개념**
- `@s`: 가로 기준 스케일링 (width 기반)
- `@vs`: 세로 기준 스케일링 (height 기반)
- `@ms`: 균형잡힌 스케일링 (폰트, 아이콘에 적합)
- `scale()`, `verticalScale()`, `moderateScale()` 함수

**장점**
- 수동 계산 불필요
- 한 번 디자인하면 모든 기기 자동 대응
- 코드 가독성 향상
- 유지보수 용이

**더 공부할 것**
- ScaledSheet의 고급 옵션
- 태블릿/폴더블 기기 대응 전략
- 다른 반응형 라이브러리와 비교

---

### React Native FlatList 최적화
**배운 것**
- `pagingEnabled`: 페이지 단위 스크롤 활성화
- `snapToInterval`: 특정 간격으로 스냅
- `snapToAlignment`: 스냅 위치 지정 (start/center/end)
- `getItemLayout`: 각 아이템의 크기와 위치 사전 정의
- `removeClippedSubviews`: 화면 밖 뷰 제거로 성능 향상
- `onViewableItemsChanged`: 화면에 보이는 아이템 추적

**성능 최적화 포인트**
- `getItemLayout` 정의 시 초기 렌더링 속도 향상
- `removeClippedSubviews`로 메모리 사용량 감소
- `scrollEventThrottle` 조정으로 스크롤 성능 개선

**더 공부할 것**
- FlatList vs ScrollView 성능 비교
- virtualizedList의 내부 동작 원리
- 대용량 리스트 최적화 기법

---

### React Native Dimensions API
**배운 것**
- `Dimensions.get('window')`: 현재 창 크기 가져오기
- `Dimensions.get('screen')`: 전체 화면 크기 (상태바 포함)
- 동적으로 화면 크기 기반 레이아웃 계산 가능

**주의사항**
- `Dimensions.get()`은 초기값만 가져옴 (회전 시 업데이트 안됨)
- 회전 대응을 위해서는 `useWindowDimensions` 훅 사용 필요
- 화면 크기 기반 조건문으로 세밀한 제어 가능

**더 공부할 것**
- `useWindowDimensions` 훅 사용법
- 화면 회전 이벤트 처리
- Safe Area 대응 방법

---

## 3. 오늘의 핵심 교훈

### 반응형 디자인의 3가지 원칙
1. **절대 고정 픽셀 값 사용하지 말 것**
   - 대신 비율, 퍼센트, 스케일링 함수 사용
   - 최소/최대 크기 제한으로 극단적인 경우 대비

2. **화면 크기별 분기 처리**
   - 3-5단계 정도로 나눠서 처리
   - 각 범위에 맞는 적절한 값 설정
   - 너무 많은 분기는 오히려 복잡도 증가

3. **중앙 정렬과 크기 제한**
   - 큰 화면에서는 최대 너비 제한
   - 남는 공간은 중앙 정렬로 처리
   - 콘텐츠가 너무 넓어지지 않도록 방지

### FlatList 사용 시 체크리스트
- [ ] `getItemLayout` 정의했는가?
- [ ] `keyExtractor` 고유값 사용하는가?
- [ ] 성능 최적화 옵션 적용했는가?
- [ ] 페이징 동작이 정확한가?
- [ ] 오버플로우 처리했는가?

### 디버깅 팁
- 화면 크기 관련 이슈는 여러 기기/시뮬레이터에서 테스트 필수
- 개발자 도구에서 레이아웃 경계선 표시 활성화
- console.log로 화면 크기와 계산된 값 확인

---

## 4. 추천 학습 자료

### 공식 문서
- [React Native FlatList](https://reactnative.dev/docs/flatlist)
- [React Native Dimensions](https://reactnative.dev/docs/dimensions)
- [react-native-size-matters GitHub](https://github.com/nirsky/react-native-size-matters)

### 유용한 아티클
- [Responsive Design in React Native - DEV Community](https://dev.to/aomuiz/responsive-design-in-react-native-building-apps-for-multiple-screen-sizes-1fnf)
- [Size Matters: How I used React Native to make my App look great on every device - Medium](https://medium.com/soluto-engineering/size-matters-5aeeb462900a)

### 검색 키워드 모음
```
react-native-size-matters tutorial
React Native responsive design 2025
FlatList performance optimization
React Native screen size best practices
React Native pagination tutorial
SafeAreaView vs View difference
React Native overflow hidden
```

---

## 5. 다음에 공부할 것

### 단기 (이번 주)
- [ ] `useWindowDimensions` 훅으로 화면 회전 대응
- [ ] 태블릿 가로 모드 레이아웃
- [ ] 다크모드 대응

### 중기 (이번 달)
- [ ] React Native Reanimated로 부드러운 애니메이션
- [ ] 제스처 핸들링 (스와이프, 핀치 등)
- [ ] 폴더블 기기 대응

### 장기
- [ ] React Native 성능 최적화 전략
- [ ] 크로스 플랫폼 디자인 시스템 구축
- [ ] 접근성(Accessibility) 개선

---

## 6. 오늘의 실수와 배운 점

### 실수 1: 처음부터 완벽한 반응형을 만들려고 함
- 한 번에 모든 화면 크기 대응하려다 복잡해짐
- **교훈**: 작은 화면부터 시작해서 점진적으로 확장하는 것이 효율적

### 실수 2: Flexbox만으로 해결하려고 함
- Flex만으로는 세밀한 제어가 어려움
- **교훈**: 조건부 스타일링과 Flex를 함께 사용해야 함

### 실수 3: 라이브러리 없이 수동으로 계산
- 처음에 Dimensions API로 직접 계산하려 함
- **교훈**: 검증된 라이브러리 사용이 더 안정적이고 빠름

---

## 7. 코드 작성 시 주의사항

### DO 
- 업계 표준 라이브러리 사용 (react-native-size-matters)
- 화면 크기별 분기 처리
- 최대/최소 크기 제한
- 중앙 정렬과 여백 활용
- 여러 기기에서 테스트

### DON'T 
- 고정 픽셀 값 하드코딩
- 한 기기에서만 테스트
- Flexbox만으로 모든 것 해결하려 함
- 라이브러리 없이 처음부터 구현
- 너무 많은 분기 조건 사용

---

## 8. 회고

오늘은 React Native에서 반응형 디자인을 제대로 구현하는 방법을 배웠다. 처음에는 단순히 Flexbox와 퍼센트만 사용하면 될 줄 알았는데, 실제로는 화면 크기별 조건 분기, 최대/최소 크기 제한, 스케일링 라이브러리 등 다양한 기법이 필요했다.

특히 `react-native-size-matters` 라이브러리는 정말 유용했다. 수동으로 계산하면 복잡하고 오류가 생기기 쉬운데, 이 라이브러리를 사용하니 코드도 깔끔하고 유지보수도 쉬워졌다.

FlatList 관련해서도 많이 배웠다. `scrollToIndex`가 불안정하다는 것, `getItemLayout`의 중요성, 그리고 다양한 최적화 옵션들을 알게 되었다.

앞으로는 처음부터 반응형을 고려해서 설계하고, 검증된 라이브러리를 적극 활용해야겠다.
