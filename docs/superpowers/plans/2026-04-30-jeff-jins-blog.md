# Jeff-Jins.github.io Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Stand up `https://jeff-jins.github.io` — a Jekyll/Chirpy static site with a short English intro home, Korean tech blog, English resume page, giscus comments, deployed via GitHub Pages.

**Architecture:** chirpy-starter 템플릿 기반의 GitHub Pages 사이트. 로컬 클론 → 설정 + 페이지 작성 → push → GitHub Actions가 자동 빌드/배포. 댓글은 giscus(GitHub Discussions) 연결.

**Tech Stack:** Jekyll, Chirpy theme (chirpy-starter), GitHub Pages, GitHub Actions, giscus, Ruby 3.x (선택, 로컬 미리보기용).

---

## File Structure

| File | Responsibility |
|---|---|
| `_config.yml` | 사이트 메타, 테마 옵션, 소셜 링크, giscus 설정 |
| `_tabs/about.md` | 짧은 영문 자기소개 (Chirpy 기본 탭) |
| `_tabs/resume.md` | 영문 이력서 (신규 탭, sidebar order=2) |
| `_includes/home-intro.html` | 홈 상단 인트로 + Resume CTA 부분 |
| `_layouts/home.html` | Chirpy 기본 home 레이아웃 위에 인트로 include 한 줄 추가 |
| `_posts/2026-04-30-hello-world.md` | 첫 포스트 (한국어) |
| `.github/workflows/pages-deploy.yml` | (chirpy-starter 동봉) GitHub Actions 빌드/배포 워크플로우 — 수정 안 함 |

---

## Task 0: Pre-flight Environment Check

**Files:** None (read-only checks).

- [ ] **Step 1: `gh` CLI 인증 확인**

Run: `gh auth status`
Expected: 로그인된 계정과 scopes 표시. 인증 안 되어 있으면 사용자에게 채팅에서 `! gh auth login` 실행 요청 (interactive 명령이라 직접 실행 못 함).

- [ ] **Step 2: git 전역 config 확인**

Run: `git config --global user.name && git config --global user.email`
Expected: 이름과 이메일 출력. 없으면 설정:

```bash
git config --global user.name "Jeff-Jins"
git config --global user.email "tjq2702@gmail.com"
```

- [ ] **Step 3: Ruby/Bundler 유무 확인 (선택)**

Run: `ruby --version && bundle --version`
- 있으면: 로컬 미리보기 가능. 메모.
- 없으면: 그냥 진행. 푸시 빌드로 검증한다.

- [ ] **Step 4: 프로젝트 폴더 확인**

Run: `ls /c/Users/jinseopkim/projects/Jeff-Jins.github.io`
Expected: `docs/` 서브디렉토리가 있어야 함 (브레인스토밍 단계에서 생성됨).

---

## Task 1: Create Repo from chirpy-starter

**Files:** None locally yet (creates remote repo, then clones).

- [ ] **Step 1: chirpy-starter 템플릿으로 레포 생성**

```bash
gh repo create Jeff-Jins/Jeff-Jins.github.io \
  --template cotes2020/chirpy-starter \
  --public \
  --description "Resume + tech blog of Jin-seop Kim (Jeff-Jins)"
```

Expected: `https://github.com/Jeff-Jins/Jeff-Jins.github.io` URL 출력.

- [ ] **Step 2: 레포 생성 검증**

```bash
gh repo view Jeff-Jins/Jeff-Jins.github.io --json name,visibility,defaultBranchRef
```

Expected: `name: "Jeff-Jins.github.io"`, `visibility: "PUBLIC"`, `defaultBranchRef.name: "main"`.

- [ ] **Step 3: 임시 위치에 클론**

```bash
cd /c/Users/jinseopkim/projects
gh repo clone Jeff-Jins/Jeff-Jins.github.io _chirpy_tmp
```

Expected: `_chirpy_tmp/` 디렉토리에 `_config.yml`, `_tabs/`, `_posts/`, `.github/workflows/pages-deploy.yml` 있음.

- [ ] **Step 4: 기존 프로젝트 폴더(`docs/` 보존)와 병합**

```bash
cd /c/Users/jinseopkim/projects
shopt -s dotglob
cp -r _chirpy_tmp/* Jeff-Jins.github.io/
cp -r _chirpy_tmp/.git Jeff-Jins.github.io/.git
cp _chirpy_tmp/.gitignore Jeff-Jins.github.io/.gitignore 2>/dev/null || true
rm -rf _chirpy_tmp
```

> `cp -r .git`로 git 메타데이터 옮겨서 기존 폴더가 그대로 git working tree가 됨.

- [ ] **Step 5: working tree 검증 + `docs/` 추가 커밋**

```bash
cd /c/Users/jinseopkim/projects/Jeff-Jins.github.io
git status
```

Expected: `docs/` 디렉토리(spec/plan 문서)가 untracked로 나옴.

```bash
git add docs/
git commit -m "docs: add design spec and implementation plan"
```

---

## Task 2: Configure `_config.yml`

**Files:**
- Modify: `_config.yml`

- [ ] **Step 1: `_config.yml` 사이트 정체성 키 업데이트**

해당 키들을 다음 값으로 설정 (chirpy-starter 기본값을 덮어쓴다):

```yaml
lang: en
timezone: Asia/Seoul

title: Jeff-Jins
tagline: Engineer at LG Electronics — webOS / AI agent infrastructure
description: >-
  Resume and learning notes by Jin-seop Kim (Jeff-Jins).
  Notes on web platforms, AI agent infra, and tech I'm picking up.

url: "https://jeff-jins.github.io"

github:
  username: Jeff-Jins

social:
  name: Jin-seop Kim
  email: tjq2702@gmail.com
  links:
    - https://github.com/Jeff-Jins

theme: jekyll-theme-chirpy
```

- [ ] **Step 2: giscus 블록 placeholder 채우기 (실 ID는 Task 8에서)**

`comments:` 섹션을 다음과 같이 수정:

```yaml
comments:
  provider: giscus
  giscus:
    repo: Jeff-Jins/Jeff-Jins.github.io
    repo_id: ""
    category: "General"
    category_id: ""
    mapping: pathname
    strict: 0
    input_position: bottom
    lang: en
    reactions_enabled: 1
```

- [ ] **Step 3: 커밋**

```bash
git add _config.yml
git commit -m "chore: configure site identity and giscus placeholders"
```

---

## Task 3: Write About Page

**Files:**
- Modify: `_tabs/about.md`

- [ ] **Step 1: `_tabs/about.md` 전체 내용 교체**

```markdown
---
icon: fas fa-info-circle
order: 4
---

Hi, I'm **Jin-seop Kim** (Jeff-Jins). I work at LG Electronics on webOS and AI agent infrastructure.

This site is where I keep my resume and short write-ups on things I'm learning — browser/runtime internals, agent runtimes, and the platform pieces that hold them together.

- 📄 [Full Resume](/resume/)
- 💻 [GitHub](https://github.com/Jeff-Jins)
- ✉️ [tjq2702@gmail.com](mailto:tjq2702@gmail.com)
```

- [ ] **Step 2: 커밋**

```bash
git add _tabs/about.md
git commit -m "docs: write About page"
```

---

## Task 4: Add Resume Page (placeholders)

**Files:**
- Create: `_tabs/resume.md`

> 개인/경력 사실은 HTML 주석(`<!-- ... -->`)으로 명시적 placeholder 처리. Task 9에서 사용자 정보를 받아 이 주석들을 실제 내용으로 교체한다.

- [ ] **Step 1: `_tabs/resume.md` 신규 생성**

```markdown
---
title: Resume
icon: fas fa-id-card
order: 2
---

# Jin-seop Kim

**Engineer · LG Electronics · Seoul, South Korea**
[tjq2702@gmail.com](mailto:tjq2702@gmail.com) · [github.com/Jeff-Jins](https://github.com/Jeff-Jins)

> Engineer focused on webOS and AI agent infrastructure. Interested in the seam between runtime platforms and the agents that run on top of them.

---

## Experience

### LG Electronics — Engineer
*<!-- start–end (e.g., 2021–Present) -->*

- <!-- bullet 1: impact-focused, what you built/improved and the result -->
- <!-- bullet 2 -->
- <!-- bullet 3 -->

---

## Projects

### <!-- Project Name -->
*<!-- One-line description. Stack: ___. Result: ___. -->*

### <!-- Project Name -->
*<!-- One-line description. Stack: ___. Result: ___. -->*

---

## Skills

- **Languages:** <!-- e.g., TypeScript, JavaScript, C++, Python -->
- **Frameworks / Runtimes:** <!-- e.g., Node.js, React, webOS -->
- **Infra / Tools:** <!-- e.g., Docker, GitHub Actions, AWS -->

---

## Education

### <!-- University Name -->
*<!-- Degree, Major — start–end -->*
```

- [ ] **Step 2: 커밋**

```bash
git add _tabs/resume.md
git commit -m "feat: add resume page (English, placeholders)"
```

---

## Task 5: Customize Home (Intro + Resume CTA)

Chirpy의 home 레이아웃은 자동으로 최근 글 카드를 출력한다. 그 위에 인트로 + Resume CTA를 얹기 위해 (1) include 파일 작성 (2) Chirpy의 home 레이아웃을 override해서 맨 위에 include 한 줄만 추가한다.

**Files:**
- Create: `_includes/home-intro.html`
- Create: `_layouts/home.html`

- [ ] **Step 1: `_includes/home-intro.html` 생성**

```html
<section class="home-intro mb-4">
  <h1 class="mb-2">Hi, I'm Jin-seop Kim 👋</h1>
  <p class="lead">
    Engineer at <strong>LG Electronics</strong> working on webOS and AI agent infrastructure.
    This site holds my resume and short notes on what I'm learning.
  </p>
  <a href="{{ '/resume/' | relative_url }}" class="btn btn-outline-primary">
    📄 Full Resume →
  </a>
</section>
<hr/>
```

- [ ] **Step 2: Chirpy의 원본 `_layouts/home.html`을 찾아서 그대로 복사**

Chirpy gem은 `bundle install` 후 다음 위치에 있다:

```bash
bundle info --path jekyll-theme-chirpy
```

출력 경로의 `_layouts/home.html`을 우리 레포의 `_layouts/home.html`로 복사:

```bash
cp "$(bundle info --path jekyll-theme-chirpy)/_layouts/home.html" _layouts/home.html
```

> Ruby/Bundler가 없으면: 같은 파일을 [Chirpy GitHub master `_layouts/home.html`](https://github.com/cotes2020/jekyll-theme-chirpy/blob/master/_layouts/home.html)에서 raw로 받아 저장한다 (`curl -o _layouts/home.html https://raw.githubusercontent.com/cotes2020/jekyll-theme-chirpy/master/_layouts/home.html`).

- [ ] **Step 3: 복사한 `_layouts/home.html` 맨 위에 home-intro include 추가**

파일 최상단(front matter `---` 블록 바로 아래)에 한 줄 삽입:

```liquid
{% include home-intro.html %}
```

즉 파일이 다음 형태가 되어야 함:

```liquid
---
layout: default
refactor: true
---

{% include home-intro.html %}

{% include_cached lang.html lang=page.lang %}

{% assign all_pinned = site.posts | where: 'pin', 'true' %}
{% comment %}
  ... (이하 Chirpy 원본 그대로) ...
{% endcomment %}
```

`...` 부분은 Step 2에서 복사한 원본을 변경 없이 보존.

- [ ] **Step 4: 로컬 미리보기 (Ruby 있을 때만)**

```bash
bundle install
bundle exec jekyll serve
```

`http://127.0.0.1:4000` 열고 인트로 + Resume CTA + 최근 글 카드 확인. Ctrl+C로 종료.

Ruby 없으면: 스킵 후 push 빌드로 검증.

- [ ] **Step 5: 커밋**

```bash
git add _includes/home-intro.html _layouts/home.html
git commit -m "feat: add intro + resume CTA on home"
```

---

## Task 6: First Post (Korean)

**Files:**
- Create: `_posts/2026-04-30-hello-world.md`

- [ ] **Step 1: 첫 포스트 작성**

```markdown
---
title: "Hello, World — 블로그 시작"
date: 2026-04-30 14:00:00 +0900
categories: [Meta]
tags: [intro]
---

이 블로그는 제가 LG전자에서 일하면서 배우고 있는 내용을 정리하기 위한 공간입니다.

주로 다룰 주제:

- **webOS** — 웹 기반 OS의 런타임/플랫폼 이슈
- **AI agent infra** — LLM 에이전트가 실제 환경에서 동작하는 데 필요한 인프라
- **개발 도구 / 워크플로우** — 매일 쓰는 것들에 대한 짧은 정리

이력서는 [/resume](/resume/)에서 볼 수 있습니다.
```

- [ ] **Step 2: 커밋**

```bash
git add _posts/2026-04-30-hello-world.md
git commit -m "feat: first post"
```

> **수식이 필요한 글 작성 시:** Chirpy는 site-wide MathJax 토글 대신 post 단위로 활성화한다. front matter에 `math: true`를 추가하면 자동으로 MathJax가 로드됨. 예:
>
> ```yaml
> ---
> title: "..."
> math: true
> ---
> ```
>
> 첫 포스트는 수식이 없으므로 활성화하지 않는다.

---

## Task 7: Push and Verify Build

**Files:** None.

- [ ] **Step 1: Push**

```bash
git push origin main
```

- [ ] **Step 2: GitHub Actions 빌드 확인**

```bash
gh run watch
```

Expected: `pages-deploy.yml` (또는 chirpy-starter가 부르는 이름) 워크플로우가 1-2분 내 성공.

실패 시 로그:
```bash
gh run view --log-failed
```

- [ ] **Step 3: GitHub Pages 활성화 + source가 Actions인지 확인**

```bash
gh api repos/Jeff-Jins/Jeff-Jins.github.io/pages
```

Expected: JSON에 `"build_type": "workflow"`, `"status": "built"` 포함.

활성화 안 되어 있으면:

```bash
gh api -X POST repos/Jeff-Jins/Jeff-Jins.github.io/pages \
  -f "build_type=workflow"
gh run rerun
gh run watch
```

- [ ] **Step 4: 라이브 URL 응답 확인**

```bash
curl -sI https://jeff-jins.github.io | head -1
```

Expected: `HTTP/2 200`. 404면 30초 대기 후 재시도 (CDN 전파).

- [ ] **Step 5: 브라우저 시각 검증**

`https://jeff-jins.github.io` 열고 다음 모두 확인:
- 인트로 + "📄 Full Resume →" 버튼
- 최근 글에 "Hello, World — 블로그 시작" 카드
- 좌측 사이드바: Home / Categories / Tags / Archives / Resume / About
- `/resume` — 영문 이력서 (placeholder 형태로라도 렌더됨)
- `/about` — About 페이지
- 우상단 다크모드 토글 동작
- 우상단 검색 아이콘 클릭 → 검색창 열림 → "Hello" 검색 → 첫 글 나옴

---

## Task 8: Enable Discussions and Wire giscus

**Files:**
- Modify: `_config.yml` (giscus `repo_id`, `category_id` 채움)

- [ ] **Step 1: 레포에 Discussions 활성화**

```bash
gh api -X PATCH repos/Jeff-Jins/Jeff-Jins.github.io \
  -F has_discussions=true
```

Expected: 응답에 `"has_discussions": true` 포함.

- [ ] **Step 2: giscus GitHub App 설치 (수동/웹)**

브라우저에서 https://github.com/apps/giscus 열고 "Install" → "Only select repositories" → `Jeff-Jins.github.io` 선택 → Install. (~30초)

> 사용자에게 채팅에서 이 단계를 직접 해달라고 요청하고, 완료 시 알려달라고 한다.

- [ ] **Step 3: giscus configurator에서 ID 두 개 받기**

브라우저에서 https://giscus.app 열고 입력:
- Repository: `Jeff-Jins/Jeff-Jins.github.io`
- Page ↔ Discussions Mapping: **pathname**
- Discussion Category: **General**

페이지 하단에 생성된 `<script>` 태그에서:
- `data-repo-id="R_kgDO..."` → repo_id
- `data-category-id="DIC_kwDO..."` → category_id

> 사용자가 두 값을 채팅으로 전달하면 다음 단계로 진행.

- [ ] **Step 4: `_config.yml` 업데이트**

`comments.giscus.repo_id`와 `comments.giscus.category_id`를 받은 값으로 채움. 다른 키는 그대로.

- [ ] **Step 5: 커밋 + 푸시 + 빌드 확인**

```bash
git add _config.yml
git commit -m "feat: wire giscus comments"
git push
gh run watch
```

- [ ] **Step 6: 댓글 박스 렌더링 확인**

`https://jeff-jins.github.io/posts/hello-world/` (정확한 슬러그는 `/posts/`에서 확인) 열고 맨 아래로 스크롤. Expected: "Sign in with GitHub to comment" 형태의 giscus 박스 노출.

오류 ("discussion not found" 등) 발생 시 Step 3 ID들을 다시 확인.

---

## Task 9: Resume Content (사용자 직접 작성, 배포 후 진행)

> **이 task는 자동화하지 않는다.** 사용자가 본인 정보를 직접 채울 예정. plan 실행 시 Task 8까지만 자동 진행하고 종료한다. 이력서는 placeholder 구조 그대로 배포된 상태에서 마무리.
>
> 사용자가 나중에 채울 때 사용할 수 있는 두 방법:
>
> **방법 1 — GitHub 웹 에디터**
> 1. https://github.com/Jeff-Jins/Jeff-Jins.github.io/blob/main/_tabs/resume.md 열기
> 2. 우상단 ✏️ (pencil) 아이콘으로 편집
> 3. HTML 주석(`<!-- ... -->`)을 실제 내용으로 교체
> 4. "Commit changes" → 1-2분 후 자동 재배포
>
> **방법 2 — 로컬 편집**
> ```bash
> cd /c/Users/jinseopkim/projects/Jeff-Jins.github.io
> # 에디터로 _tabs/resume.md 수정
> git add _tabs/resume.md
> git commit -m "docs: fill in resume"
> git push
> ```
>
> 검증: 배포 후 `https://jeff-jins.github.io/resume/` 열어서 모든 섹션이 실제 내용으로 보이고 `<!-- -->` 주석이 본문에 새지 않는지 확인.

---

## Task 10: Final Acceptance

Spec Section 10 (Success Criteria)을 그대로 점검:

- [ ] `https://jeff-jins.github.io` HTTP 200
- [ ] 홈에 인트로 + Latest Posts + "📄 Full Resume →"
- [ ] `/resume`에 실제 영문 이력서 (Task 9 보류 시: placeholder 형태로라도 렌더)
- [ ] 첫 포스트 1편 발행
- [ ] giscus 댓글 박스 노출 (포스트 하단)
- [ ] 다크모드 토글 동작
- [ ] 검색에서 첫 포스트 검색됨
- [ ] 모바일 viewport (devtools 반응형 모드)에서 가로 스크롤 없이 렌더
- [ ] 사이드바 네비: Home / Categories / Tags / Archives / Resume / About

전부 통과하면 ✅ 완료. 스냅샷이 필요하면:

```bash
git tag v0.1.0 -m "first deploy"
git push --tags
```

---
