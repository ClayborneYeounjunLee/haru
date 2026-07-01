# 하루 · Haru — 루틴 · 할 일 앱

> 매일의 루틴을 한 곳에서, 가볍게 체크하는 단일 파일 웹앱. 루틴 달성률(링/잔디), 할 일, 디데이/디플러스, 연속 스크롤 캘린더를 하나로 묶었습니다.

🔗 **라이브:** https://clayborneyeounjunlee.github.io/haru/
📦 **저장소:** https://github.com/ClayborneYeounjunLee/haru

---

## ✨ 주요 기능

- **오늘(루틴) 화면** — 오늘 달성률을 큰 원형 링(SVG)으로 보여주고, 루틴 항목마다 카운터(＋/−), 체크(◯/✓), 시간(분, 타이머 ▶/⏸ 포함), 메모(자유 입력), 그룹(여러 항목 중 "모두" 또는 "하나만" 완료) 방식으로 기록합니다.
- **날짜 이동** — 오늘 화면 상단 화살표(‹ ›)로 과거·미래 날짜를 넘겨 그날의 루틴을 따로 기록/열람하고, "↩ 오늘로" 버튼으로 복귀합니다.
- **분 단위 타이머** — `minutes` 유형 루틴은 ▶ 를 눌러 실시간 스톱워치를 돌리고, ⏸ 로 멈추면 경과 시간(분)이 그날 기록에 더해집니다.
- **할 일(To-do)** — 중요도(낮음/보통/높음)와 마감일(D-day 칩)로 관리하고, "마감순 / 중요도순" 정렬 토글을 제공합니다. 그룹으로 묶을 수 있고, 완료 항목은 하단에 모아 일괄 삭제할 수 있습니다.
- **디데이 · 디플러스** — 시험·여행 같은 미래 목표는 **디데이(남은 날, D-7)**, 기념일 같은 지난 날은 **디플러스(지난 날부터, D+n / n일째)**로 추가합니다. 10종 아이콘 선택 지원.
- **캘린더** — 최근 12개월(가입 달 이후)을 하나로 이어 붙인 **연속 스크롤 달력**. 칸 색은 그날 달성률로 진해지고(잔디 tint), 마감 할 일은 점(dot), 디데이는 아이콘으로 표시됩니다. "전체 / 루틴별" 보기를 전환하며 이번 달 평균·연속·100%·기록일 통계를 함께 봅니다. 날짜를 탭하면 그날 상세(루틴별 기록·디데이·할 일) 시트가 열립니다.
- **설정** — 루틴 항목 추가/수정/삭제/순서 변경, 언어(한/영) 토글, 다크 모드, 데이터 내보내기(JSON 백업), 기록 초기화, 로그인/로그아웃.
- **로그인 2가지** — Google 로그인(클라우드 저장, 여러 기기에서 이어쓰기) 또는 로그인 없이 이 기기에서만 쓰기(게스트·localStorage).
- **다국어(i18n)** — 한국어 / English 완전 토글. 앱 전역 문자열이 두 언어로 정의되어 있습니다.
- **다크 모드** — 수동 토글 + 시스템 설정(`prefers-color-scheme`) 자동 감지. FOUC 방지를 위해 `<head>`에서 테마를 먼저 적용합니다.
- **PWA 지향** — 애플 홈화면 추가 메타, `theme-color`, `apple-touch-icon`(icon-180.png), 인라인 SVG 파비콘. `viewport-fit=cover`와 `env(safe-area-inset-*)`로 노치 대응.

---

## 🧱 기술 스택 / 언어

| 항목 | 내용 |
|---|---|
| 언어 | 순수 **HTML + CSS + JavaScript (ES 모듈)** — 프레임워크 없음 |
| 파일 구성 | **단일 파일** `index.html` (약 1,513줄, HTML·CSS·JS 전부 인라인) |
| 빌드 도구 | **없음** (번들러·트랜스파일러·package.json 없음). 브라우저에서 그대로 실행 |
| 모듈 방식 | 메인 스크립트가 `<script type="module">` — Firebase SDK를 동적 `import()` 로 로드 |
| 스타일 | 인라인 `<style>` + **CSS 변수** 기반 테마(`:root` / `html[data-theme="dark"]`) |
| 폰트 | 시스템/웹폰트 폴백 스택: `Pretendard`, `Apple SD Gothic Neo`, `Malgun Gothic`, `Noto Sans KR`, sans-serif (별도 CDN 폰트 로드는 없음) |
| 아이콘 | 이모지 + 인라인 SVG(구글 로고, 달성 링 그라디언트, 파비콘) |
| 백엔드 SDK | **Firebase JS SDK v12.14.0** — `firebase-app`, `firebase-auth`, `firebase-firestore` 3개 모듈을 gstatic CDN에서 로드 |
| 저장/동기화 | Firebase **Authentication(Google)** + **Cloud Firestore**(오프라인 영속 캐시 사용) / 게스트는 localStorage |

### CDN·SDK 로드 방식 (실제 코드)

```js
const FB_VER = "12.14.0";
const base = "https://www.gstatic.com/firebasejs/" + FB_VER + "/";
const [appM, authM, fsM] = await Promise.all([
  import(base + "firebase-app.js"),
  import(base + "firebase-auth.js"),
  import(base + "firebase-firestore.js")
]);
```

Firestore는 오프라인 캐시와 롱폴링을 켜서 초기화합니다:

```js
const db = fsM.initializeFirestore(app, {
  experimentalForceLongPolling: true,
  localCache: fsM.persistentLocalCache({ tabManager: fsM.persistentSingleTabManager() })
});
```

---

## 🏗️ 시스템 구조

### 부팅 흐름

1. `<head>`의 즉시 실행 스크립트가 `localStorage["haru-theme"]`(또는 시스템 선호)를 읽어 `<html data-theme>`를 먼저 세팅 → 다크모드 깜빡임 방지.
2. 본문 하단 `<script type="module">`이 실행되며 `showLoading()`으로 로딩 화면을 띄우고 `applyLang(L)`로 언어를 적용.
3. `initFirebase()` — apiKey가 `AIza…` 형태면 Firebase SDK를 동적 import 후 App/Auth/Firestore 초기화. 실패하거나 미설정이면 게스트 전용으로 폴백.
4. 성공 시 `onAuthStateChanged` 등록:
   - 로그인 사용자 있으면 → `startCloud()` (Firestore 문서 로드/시드 후 앱 진입).
   - 없으면 → 이전에 게스트였으면 `startGuest()`, 아니면 로그인 화면.
5. 미설정/네트워크 실패 시 → 로그인 화면에 "Firebase 설정 전" 안내 노출, 게스트로 이용 가능.

### 화면 & 라우팅

SPA이며 라우터 없이 **화면 div 표시/숨김**으로 전환합니다. 화면 id: `screen-loading`, `screen-auth`, `screen-today`, `screen-todo`, `screen-dday`, `screen-calendar`, `screen-settings`.

```
showTab(t)  →  hideAll() → 해당 screen 표시 → 탭바 active 갱신 → 렌더 함수 호출
```

| 탭 | 화면 | 렌더 함수 |
|---|---|---|
| 오늘 | `screen-today` | `renderToday()` |
| 할 일 | `screen-todo` | `renderTodos()` |
| 디데이 | `screen-dday` | `renderDdays()` |
| 캘린더 | `screen-calendar` | `renderCalendar()` |
| 설정 | `screen-settings` | `renderSettings()` |

### 상태 관리

- 전역 단일 객체 `state` 에 모든 사용자 데이터를 보관(아래 "데이터" 참고). `mode`(`guest`/`cloud`), `uid`, `viewDate`, `curTab`, `todoSort`, `calY/calM/calRoutine` 등 UI 상태는 별도 전역 변수.
- 렌더는 각 렌더 함수가 `innerHTML` 문자열을 만들어 주입하고 이벤트를 재바인딩하는 **명령형 재렌더** 방식(가상 DOM 없음).
- `normalize(s)` 가 로드된 데이터에 누락 필드를 기본값으로 채워 스키마를 항상 일관되게 유지.
- 저장은 **디바운스 자동 저장**: `save()` 호출 시 게스트는 즉시 localStorage, 클라우드는 `dirty` 표시 후 2초 뒤 `flushSave()`가 Firestore에 `setDoc(..., {merge:true})`. 탭 숨김(`visibilitychange`)·페이지 종료(`pagehide`) 시 즉시 flush.

### 핵심 루틴 엔진 (달성률 계산)

- `doneness(item, v)` — 루틴 1개의 달성도(0~1)를 유형별로 계산(카운터/분은 목표 대비 비율, 체크는 0/1, 그룹은 all=완료비율·any=하나라도면 1, 메모는 통계 제외).
- `dayStats(dk)` — 그날 전체 루틴 평균 달성률(%)·완료 개수 반환 → 원형 링과 캘린더 tint의 근거.
- `itemFill(item, v)` — 루틴별 캘린더/통계용. `doneness`와 달리 메모도 글이 있으면 달성으로 간주.
- `grassLevel(pct)` / `tint(pct)` — 잔디·캘린더 칸 색 단계 계산.

---

## 🗂️ 데이터

모든 데이터는 하나의 `state` 객체에 담깁니다. 기본 구조(`freshState()`):

```js
{
  nick: "하루",              // 표시 이름 (구글 로그인 시 displayName로 대체)
  created: "2026-07-01",     // 가입/시작 날짜 (YYYY-MM-DD) — 캘린더 통계 시작점
  routine: [ ...DEFAULT_ROUTINE ],  // 루틴 항목 정의 배열
  logs: {},                  // 날짜별 루틴 기록  { "2026-07-01": { <itemId>: <값> } }
  todos: [],                 // 할 일 배열
  ddays: [],                 // 디데이/디플러스 배열
  todoGroups: []             // 할 일 그룹 배열
}
```

### 루틴 항목(routine item) 스키마

| 필드 | 설명 |
|---|---|
| `id` | 고유 id (기본 루틴은 문자열, 사용자 추가분은 `uid7()` 랜덤) |
| `type` | `counter` \| `check` \| `minutes` \| `note` \| `group` |
| `icon` | 이모지/문자 아이콘 |
| `name` | 표시 이름 |
| `target` | (counter/minutes) 목표 횟수·분 |
| `placeholder` | (note) 안내 문구 |
| `goal` | (group) `all`(모두) \| `any`(하나만) |
| `options` | (group) `[{ id, name }]` 하위 항목 목록 |

**기본 루틴 9종**(`DEFAULT_ROUTINE`, 예시로 일본어 학습·영양제·운동 등이 들어있음):

| id | type | icon | name | 비고 |
|---|---|---|---|---|
| `duolingo` | counter | 🦉 | 듀오링고 일본어 | target 4 |
| `hira` | counter | あ | 히라가나 | target 4 |
| `kata` | counter | ア | 가타카나 | target 4 |
| `supp` | group | 💊 | 영양제 | goal all · 오메가3/비타민D/유산균 |
| `ads` | group | 📺 | 광고 보기 | goal all · 카카오페이/네이버페이/토스 |
| `reading` | minutes | 📖 | 독서 | target 30분 |
| `study` | note | 📝 | 오늘 공부한 것 | 자유 입력 |
| `exercise` | group | 💪 | 운동 | goal any · 가슴·삼두/등·이두/유산소 |
| `mask` | check | 🧖 | 마스크팩 | on/off |

### `logs` 값 형태 (유형별)

| type | 저장 값 예시 |
|---|---|
| counter / minutes | 숫자 (`3`) |
| check | 불리언 (`true`) |
| note | 문자열 (`"수학 2단원"`) |
| group | 객체 (`{ s1: true, s2: false }`) |

### 할 일(todo) 스키마

```js
{ id, text, pr, due, done, doneAt, gid }
```

| 필드 | 설명 |
|---|---|
| `pr` | 중요도 0(낮음)/1(보통)/2(높음) |
| `due` | 마감일 `YYYY-MM-DD`(선택) |
| `done` / `doneAt` | 완료 여부 / 완료 시각(ms) |
| `gid` | 소속 그룹 id(없으면 빈 문자열) |

### 디데이/디플러스(dday) 스키마

```js
{ id, title, date, icon, kind }   // kind: "down"(디데이) | "up"(디플러스)
```

### 할 일 그룹(todoGroup) 스키마

```js
{ id, name }
```

---

## 💾 저장소 / DB

이 앱은 **하이브리드 저장**입니다. 로그인하면 클라우드(Firestore), 아니면 이 기기(localStorage)에 저장합니다.

### 1) 클라우드 — Cloud Firestore

- **Firebase 프로젝트:** `haru-221ae`
- **컬렉션 / 문서:** `haru` 컬렉션의 `haru/{uid}` 문서 하나에 사용자의 전체 `state`를 통째로 저장(문서당 사용자 1명).
  ```js
  FB.setDoc(FB.doc(FB.db, "haru", uid), state, { merge: true });
  ```
- **읽기 흐름:** 로그인 시 먼저 로컬 캐시(`getDocFromCache`)로 즉시 진입 → 이후 서버 문서를 타임아웃(6~20초)으로 가져와 갱신. 문서가 없으면 게스트 데이터를 시드해 새로 생성.
- **오프라인:** `persistentLocalCache`로 오프라인에서도 읽기/쓰기 후 재접속 시 동기화.

**권장 Firestore 보안 규칙** — 각 사용자가 자기 문서만 읽고 쓰도록:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /haru/{uid} {
      allow read, write: if request.auth != null && request.auth.uid == uid;
    }
  }
}
```

> 참고: 코드의 `firebaseConfig.apiKey`(AIza…)는 **공개용 웹 설정값**으로, 노출되어도 무방한 식별자입니다(코드 주석도 이 점을 명시). 실제 보안은 위 Firestore 규칙과 Firebase Auth가 담당합니다.

### 2) 게스트/오프라인 — localStorage

로그인 없이 쓰면 전체 `state`가 브라우저 localStorage에 저장됩니다. 사용 키:

| 키 | 용도 |
|---|---|
| `haru-guest` | 게스트 모드의 전체 `state`(JSON 직렬화) |
| `haru-mode` | 마지막 이용 모드 (`"guest"` / `"cloud"`) — 재방문 시 자동 진입 판단 |
| `haru-theme` | 테마 (`"light"` / `"dark"`) |
| `haru-lang` | 언어 (`"ko"` / `"en"`) |

> 게스트→로그인 전환 시, 기존 `haru-guest` 데이터를 시드로 삼아 클라우드 문서를 만들 수 있습니다(`seedState`). 로그아웃하면 `haru-mode`를 지우고 로그인 화면으로 돌아갑니다.

### 백업 / 초기화

- **내보내기:** 설정 → "데이터 내보내기"가 `state`를 `haru-backup-YYYY-MM-DD.json` 파일로 다운로드.
- **초기화:** 설정 → "기록 초기화"가 확인 후 `freshState()`로 되돌림.

---

## 🌐 외부 API · 의존성

| 의존성 | 용도 | 키 필요 | 위치 |
|---|---|---|---|
| Firebase App/Auth/Firestore SDK v12.14.0 | 로그인·클라우드 저장 | 웹 config 필요(공개 식별자) | `index.html` 상단 `FIREBASE_CONFIG` 객체 |
| Google 로그인 (Firebase `GoogleAuthProvider`) | 소셜 로그인 | Firebase 콘솔에서 Google 공급자 활성화 | — |
| gstatic CDN (`www.gstatic.com/firebasejs/...`) | SDK 모듈 로드 | 불필요 | 동적 `import()` |

- 그 외 외부 API(지도/환율/음성 등)는 **사용하지 않습니다.**
- Firebase가 미설정(`apiKey`가 `AIza`로 시작하지 않음)이거나 네트워크로 SDK 로드 실패 시, 앱은 자동으로 **게스트 전용 모드**로 동작하고 로그인 화면에 안내를 띄웁니다.

---

## ▶️ 로컬 실행 방법

빌드가 필요 없는 정적 파일입니다. 단, ES 모듈 `import()`과 Firebase 팝업 로그인 때문에 **`file://`가 아니라 로컬 HTTP 서버**로 여는 것을 권장합니다.

```bash
# 저장소 폴더에서 (아무 정적 서버나 사용)
python -m http.server 5173
#  → http://localhost:5173  접속

# 또는 Node가 있으면
npx serve .
```

- 게스트 모드는 서버 없이도 대부분 동작하지만, Google 로그인은 Firebase 콘솔에 **승인된 도메인**(예: `localhost`, GitHub Pages 도메인)이 등록되어 있어야 합니다.
- `package.json`이 없으므로 npm 스크립트는 없습니다.

---

## 🚀 배포

**GitHub Pages** 로 배포됩니다.

1. 이 저장소를 GitHub에 푸시.
2. Settings → Pages → Source를 `main` 브랜치(루트 `/`)로 지정.
3. 배포 주소: https://clayborneyeounjunlee.github.io/haru/
4. Firebase 콘솔 → Authentication → Settings → **승인된 도메인**에 위 Pages 도메인 추가(구글 로그인용).

정적 호스팅이면 어디든(예: Firebase Hosting) 동일하게 `index.html`만 올리면 됩니다.

---

## 📁 파일 구조

```
haru/
├── index.html      # 앱 전체 — HTML·CSS·JS·i18n·Firebase 연동이 모두 이 한 파일에
└── icon-180.png    # 애플 홈화면/PWA 아이콘 (apple-touch-icon, 180×180)
```

> 저장소에는 이 두 파일만 있습니다. `package.json`, `firebase.json`, `.firebaserc`, `.gitignore`, 별도 데이터 `.js` 파일은 없습니다. Firebase 설정값은 `index.html` 내부 `FIREBASE_CONFIG`에 인라인되어 있습니다.

---

## 🔗 관련 앱 (모아 허브 · 형제 앱)

- 앱바 우측의 **◈ 버튼**은 개인 앱 허브 **"모아(moa)"**(https://clayborneyeounjunlee.github.io/moa/)로 돌아갑니다.
- 하루(Haru)는 이 앱 모음의 **원형 템플릿** 역할을 합니다 — 다크모드/i18n(한·영 토글)/바텀시트(sheet) UI/게스트-클라우드 하이브리드 저장 패턴을 형제 앱(가계부 등)이 복제해 사용합니다.

---

<sub>하루 · Haru — 단일 파일 정적 웹앱. 문서는 실제 `index.html` 코드를 기준으로 작성되었습니다.</sub>
