# 포트폴리오 파일 분리 + Vercel 배포

## Context

현재 `D:/Portfolio/portfolio.html` 한 파일(454줄)에 HTML과 CSS가 섞여 있고, 네비게이션이 존재하지 않는 라우트(`/`, `/resume`, `/portfolio`)를 가리켜 실제로 동작하지 않는 상태다. 내용도 전부 placeholder(`김지안`, `jian.kim@gmail.com`, 예시 회사명 등)이다.

이번 작업의 목표는 **구조만 정리하고 배포까지 성공**시키는 것. 실제 개인 정보(이름, 경력, 프로젝트)는 별도 작업으로 미룬다.

방향 결정 사항:
- **한 페이지 구조 유지** (개발자 포트폴리오의 일반적 패턴)
- **HTML / CSS 파일 분리** (수정 시 diff 깔끔, 혼자 CSS 튜닝하기 쉬움)
- **Vercel 배포** (GitHub 연동 자동 배포)

---

## 이번 작업 범위 (In-Scope)

### 1. 파일 구조 변경
- `portfolio.html` → `index.html` 로 이름 변경
  - 이유: Vercel/GitHub Pages 모두 기본 진입점이 `index.html`. 루트 경로(`/`)로 접근 가능해짐.
- `<style>` 블록(10~236번째 줄) → `style.css` 로 분리
- `index.html` 상단에 `<link rel="stylesheet" href="/style.css">` 추가
- `<style>...</style>` 블록 제거

결과 구조:
```
D:/Portfolio/
├── index.html        (이전 portfolio.html, <style> 제거 + <link> 추가)
├── style.css         (기존 <style> 내용 이동)
├── README.md
└── .git/
```

### 2. 네비게이션 수정 (index.html:240-246)

현재:
```html
<a href="/">Home</a>
<a href="/resume">Resume</a>
<a href="/portfolio" class="active">Portfolio</a>
<a href="https://github.com/jiankim" target="_blank" rel="noopener">Github</a>
<a href="https://velog.io/@jian" target="_blank" rel="noopener">Velog</a>
```

변경 후 (한 페이지 기준 섹션 앵커):
```html
<a href="#experience">경력</a>
<a href="#projects">프로젝트</a>
<a href="#troubleshooting">트러블슈팅</a>
<a href="https://github.com/jiankim" target="_blank" rel="noopener">Github</a>
<a href="https://velog.io/@jian" target="_blank" rel="noopener">Velog</a>
```

각 `<h2 class="section-h">` 엘리먼트에 대응되는 `id` 추가:
- `💼 경력` → `id="experience"`
- `📂 프로젝트` → `id="projects"`
- `🔧 실무 트러블슈팅` → `id="troubleshooting"`

(GitHub, Velog 링크의 URL은 placeholder 상태 유지 — 실제 정보 채우는 작업에서 교체)

### 3. Vercel 배포 설정

- **방식**: GitHub repo 연동 후 Vercel 대시보드에서 Import (CLI 아님)
- **별도 config 파일 불필요** — 순수 정적 파일이라 Vercel이 자동 감지
- 선택 사항: `vercel.json` 추가해서 `cleanUrls` 옵션 설정 가능 (지금은 불필요, 나중에 `/resume` 같은 페이지 생기면 고려)

배포 절차 (실제 구현 단계에서):
1. GitHub에 repo push (현재 로컬 git만 있음 — 원격 아직 없음)
2. Vercel 대시보드 → New Project → GitHub repo 선택
3. Framework Preset: **Other** (정적 HTML)
4. Root Directory: `./` (기본값)
5. Deploy 클릭 → `https://<project-name>.vercel.app` 에 자동 배포

### 4. 수정할 파일 목록 (실제 구현 단계에서 건드릴 것)

| 파일 | 동작 |
|---|---|
| `portfolio.html` | 삭제 |
| `index.html` | 신규 (기존 HTML 본문 + `<link>`로 CSS 참조) |
| `style.css` | 신규 (기존 `<style>` 내용 이동) |
| `README.md` | 그대로 유지 |

---

## 이번 작업 범위 밖 (Out-of-Scope, 다음 작업에서)

- 실제 개인 정보 채우기
  - 이름 `김지안` → 본인 이름
  - 이메일 `jian.kim@gmail.com` → `greatingapple01@gmail.com`
  - GitHub `jiankim` → `GyyymShark`
  - 경력사, 프로젝트, 트러블슈팅 실제 내용 교체
  - 프로필 SVG placeholder → 실제 프로필 이미지 (선택)
- Resume 페이지 분리 (필요 시 나중에 `resume.html` 추가)
- 커스텀 도메인 연결
- 다크모드, 국영문 토글 등 기능 추가

---

## Verification (배포 완료 후 확인할 것)

### 로컬 확인
1. `index.html`을 브라우저에서 직접 열어 스타일이 깨지지 않는지 확인 (`style.css` 로드 성공)
2. 네비 항목 클릭 시 해당 섹션으로 스크롤되는지 확인 (`#experience`, `#projects`, `#troubleshooting`)
3. 개발자 도구 콘솔에 404나 에러가 없는지 확인
4. 모바일 뷰(≤ 640px)에서 반응형 레이아웃 정상 동작 확인

### 배포 확인
1. Vercel 배포 URL 접속 → 루트(`/`)에서 바로 렌더링되는지
2. 네비 앵커 링크가 배포된 도메인에서도 정상 동작하는지
3. Pretendard 폰트 CDN 로드 성공 여부 (네트워크 탭에서 확인)
4. Lighthouse 점수 대략 확인 (정적 파일이라 90+ 예상)

---

## 오늘 해야 할 것

1. 이 plan 파일 생성 완료 ✓
2. plan 파일을 작업 repo(`D:/Portfolio`)에 커밋하고 GitHub로 push
   - 단, plan 파일 자체는 `C:\Users\sangw\.claude\plans\` 에 있으므로, 저장소에 포함시킬지는 사용자 결정 필요
   - 옵션 A: plan 파일을 `D:/Portfolio/PLAN.md` 로 복사해서 커밋
   - 옵션 B: plan은 개인 디렉토리에만 두고, 오늘은 "이번 대화 종료 전에 git push" 없이 마무리
3. 실제 구현은 다음 세션에서 진행

---

## Critical Files

- `D:/Portfolio/portfolio.html` (현재 — 삭제 예정)
- `D:/Portfolio/index.html` (신규)
- `D:/Portfolio/style.css` (신규)
