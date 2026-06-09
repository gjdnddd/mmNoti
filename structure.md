# mmNoti / index.html 구조 인덱스

## CSS (line 7–259)
- `line 9–28` — CSS 변수 (--bg, --blue, --green 등)
- `line 32–66` — 공통 컴포넌트 (.card, .metric, .btn-*, .prog-bar 등)
- `line 68–73` — 하단 탭바
- `line 75–77` — 토스트
- `line 79–89` — 오버레이/모달 (.overlay, .sheet, .confirm-box)
- `line 91–133` — 메인 화면 (.summary-card, .port-card, .add-fab)
- `line 135–160` — 상세 화면 (.detail-hero, .guide-card, .bottom-3btn)
- `line 161–177` — 입력 화면 (.paste-btn, .textarea, .parse-result)
- `line 179–189` — 히스토리 (.hist-item)
- `line 191–207` — 사이클 리포트 (.cycle-card, .ongoing-card)
- `line 208–231` — 설정 화면 (.port-list-item, .gist-section)
- `line 233–258` — 필터 바, 추가 기능, 주문 검증

## HTML 화면 (line 264–577)
- `line 264–288` — 탭바 (#tab-bar, 5개 탭)
- `line 289–318` — 메인 화면 (#screen-main): 요약 카드(#s-seed, #s-invested, #s-count), 포트 카드 목록(#main-cards)
- `line 319–416` — 상세 화면 (#screen-detail): 기본정보/매입정보/무매공식 카드, 거래내역 테이블, 하단 버튼
- `line 417–461` — 입력 화면 (#screen-input): 카카오톡 파싱, 무매 선택, 대기 목록
- `line 462–479` — 히스토리 (#screen-history): 필터 바, 거래 목록
- `line 480–497` — 사이클 리포트 (#screen-cycle): 필터 바, 완료/진행 목록
- `line 498–576` — 설정 (#screen-settings): 총 시드 입력, 포트 목록, Gist 연동

## HTML 오버레이 (line 578–738)
- `line 578–626` — 무매 추가/편집 시트 (#add-overlay): 이름/버전/종목/D값/시드/T값/평단 입력
- `line 627–638` — 삭제 확인 (#del-overlay)
- `line 639–661` — 사이클 종료 확인 (#end-overlay): 실현 수익, 종료 방식(기존시드/수익포함/종료만)
- `line 662–679` — 주문 검증 (#verify-overlay): HTS 엑셀 붙여넣기
- `line 680–710` — 수동 거래 입력 (#manual-overlay)
- `line 711–737` — 거래 내역 수정 (#trade-edit-overlay)

## JavaScript (line 739–2156)

### 상태 및 초기화
- `line 743–780` — STATE: `state` 객체 (portfolios, trades, pending, gistToken, gistId, lastSync, **totalSeed**), segs
- `line 782–801` — INIT: window.onload → loadLocal → syncGist → renderAll
- `line 782–803` — loadLocal / saveLocal (localStorage)

### Gist 연동
- `line 810–843` — `syncGist()` — Gist에서 불러오기 (portfolios/trades/pending/**totalSeed** 복원)
- `line 844–862` — `saveToGist()` — Gist에 저장 (**totalSeed** 포함)
- `line 863–1113` — `parseMeritzItem()`, `pollMeritz()` — 메리츠 자동 체결 감지

### 계산
- `line 1077` — `calcStarRate(p)` — 무매 공식 계산

### 렌더링
- `line 1115` — `renderAll()`
- `line 1128–1165` — `renderSummary()` — 총 시드/배정 시드/잔여 시드 표시
- `line 1166–1253` — `renderMainCards()` — 포트 카드 목록
- `line 1255–1305` — `openDetail(id)` — 상세 화면 렌더
- `line 1533–1587` — `renderHistory()`
- `line 1589–1673` — `renderCycle()`, `setCycleFilter()`, `toggleCycleTrades()`
- `line 1674–1716` — `renderSettings()`

### 입력/거래
- `line 1307–1353` — `pasteClipboard()` — 클립보드 파싱
- `line 1315–1353` — `parseKakao()` — 카카오톡 체결 파싱
- `line 1355–1395` — `confirmInput()`, `ignoreInput()` — 파싱 결과 입력 확정
- `line 1397–1407` — `openManualInput()` — 수동 입력 오버레이 열기

### 사이클 관리
- `line 1439–1531` — `endCycle()` — 사이클 종료 & **totalSeed 자동 증가**

### 포트폴리오 CRUD
- `line 1718–1727` — `openAddSheet()`
- `line 1728–1738` — `openEditSheet(id)`
- `line 1751–1793` — `savePortfolio()` — **총 시드 초과 경고**
- `line 1795–1826` — `deletePortfolio()`

### 거래 내역 수정/삭제
- `line 1890–1916` — `deleteHistoryTrade()`
- `line 1918–1976` — `openTradeEdit()`, `saveTradeEdit()`, `deleteTrade()`

### UI 유틸
- `line 1828–1841` — `switchTab()`, `showScreen()`, `goBack()`
- `line 1843–1849` — `showToast()`

### VR 대시보드 연동
```
state.vrGistId        VR 대시보드 Gist ID (localStorage 저장)
saveVrGistConfig()    설정 탭에서 VR Gist ID 저장
sendToVR(id)          TQQQ pending 항목 → VR Gist의 vr_pending.json append + pending 제거
renderPending()       TQQQ + vrGistId 설정 시 "VR로 ↗" 버튼 표시 (btn-vr 스타일)
```

## 데이터 구조

### Portfolio 객체
```js
{
  id, name, version, symbol, D, R, seed,
  t_value, avg_price, hold_qty,
  acc_profit, start_date,
  trades: [{ date, type, price, qty, amount, fee }],
  cycles: [{ no, start, end, seed, profit, trades_count, trades }],
  ended: bool
}
```

### State 객체
```js
{
  portfolios: [],
  trades: [],      // 전체 거래 내역 (히스토리용)
  pending: [],     // 미처리 체결 대기
  gistToken, gistId, lastSync,
  totalSeed: 0    // 총 운영 시드 (사용자 입력)
}
```
