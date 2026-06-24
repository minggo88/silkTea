# 하관청소타 단일 페이지 사이트 - 결과 보고

## 개요
요청된 스펙에 따라 `index.html` 단일 파일로 Silk Tealeaf 브랜드의 보이차 제품 "하관청소타" 소개 페이지를 제작했습니다. CSS/JS는 모두 해당 파일 내부에 인라인으로 포함되어 있으며, 외부 라이브러리 없이 순수 HTML/CSS/JS(Vanilla)로 구현했습니다.

## 구현 범위
- **HEADER**: 고정 헤더, 스크롤 시 반투명 블러 배경 전환, 데스크톱 내비게이션 + 모바일 햄버거 메뉴
- **HERO**: `img2.jpg` 배경 + 다크 오버레이, 타이틀/서브타이틀/브랜드 크레딧, 바운스 애니메이션 스크롤 화살표
- **PRODUCT (소개)**: 좌측 이미지 슬라이더(tea1~5.jpg, 화살표/도트 내비게이션), 우측 제품 스펙 테이블
- **STORY (스토리)**: 본문 3단락 + story1~4.jpg 이미지(2단 배치 1회 + 풀와이드 2회)
- **MASTER (티마스터)**: 메인 이미지 + 4개 썸네일 갤러리(클릭 시 전환), 안우섭 선생님 소개
- **FOOTER**: 로고(흰색 반전), 태그라인, 카피라이트
- **스크롤 애니메이션**: Intersection Observer 기반 fade-up, 형제 요소 0.1s 단위 스태거 딜레이
- **반응형**: 768px 이하에서 1단 컬럼 레이아웃 + 햄버거 메뉴로 전환

## 검증 과정
Playwright(Chromium headless)로 로컬 서버(`python -m http.server`)에 띄운 페이지를 직접 구동하여 데스크톱(1440px)·모바일(390px) 화면을 스크린샷으로 확인했습니다.

- 히어로, 제품, 스토리, 티마스터, 푸터 섹션 모두 정상 렌더링 확인
- 이미지 슬라이더 화살표/도트 동작 확인
- 모바일 햄버거 메뉴 열림/닫힘 동작 확인
- 브라우저 콘솔 에러 없음 확인

## 발견 및 수정한 버그
스크롤 애니메이션 스태거 딜레이 로직에서 버그를 발견하여 수정했습니다.

```js
// 수정 전 (버그) — DOM 엘리먼트를 객체 키로 사용하면
// "[object HTMLDivElement]" 같은 문자열로 강제 변환되어
// 페이지 내 모든 <div> 부모가 하나의 그룹으로 뒤섞임
const groups = {};
fadeEls.forEach(el => {
  const parent = el.parentElement;
  if (!groups[parent]) groups[parent] = [];
  groups[parent].push(el);
});

// 수정 후 — Map을 사용해 실제 엘리먼트 참조로 그룹핑
const groups = new Map();
fadeEls.forEach(el => {
  const parent = el.parentElement;
  if (!groups.has(parent)) groups.set(parent, []);
  groups.get(parent).push(el);
});
```

이 버그로 인해 제품 스펙 테이블, 스토리 이미지, 마스터 갤러리 등 여러 섹션의 요소들이 스크롤 진입 시 제대로 나타나지 않거나 순서가 뒤섞이는 문제가 있었으며, 수정 후 모든 섹션이 의도대로 순차 페이드인 되는 것을 확인했습니다.

## 파일
- `index.html` — 전체 페이지 (HTML+CSS+JS 단일 파일)
- `img/` — 기존 제공된 이미지 폴더 그대로 사용 (logo.png, people1.jpg, story1~4.jpg, tea1~5.jpg, img2.jpg)

## 비고
검증용 스크린샷은 임시 작업 디렉토리에 생성했으며 프로젝트 폴더에는 포함하지 않았습니다. 필요 시 다시 캡처해 드릴 수 있습니다.
