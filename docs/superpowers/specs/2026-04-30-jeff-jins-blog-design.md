---
title: Jeff-Jins.github.io — Resume + Tech Blog
date: 2026-04-30
status: design (awaiting user review)
owner: Jeff-Jins (tjq2702@gmail.com)
---

# Jeff-Jins.github.io — Resume + Tech Blog

GitHub Pages 기반 정적 사이트. 이력서/포트폴리오가 메인이고, 학습한 기술 스택을 정리하는 블로그를 부수적으로 발행한다.

## 1. Goal & Scope

**핵심 목표**
- 이력서가 메인 — 사이트 진입 시 본인 소개 + 이력서 링크가 빠르게 보이도록
- 글쓰기 마찰 최소화 — 마크다운 push → 자동 배포
- 운영 복잡도 최소 — 글쓰기와 이력서 갱신에만 집중

**범위 외 (현재 단계에서는 안 함)**
- 커스텀 도메인 — 나중에 `CNAME` 추가하면 됨
- 트래픽 분석 — 운영해보고 필요해지면 추가
- 다국어 라우팅 시스템 — 글마다 언어 다르게 쓰는 정도로 충분
- PDF 이력서 자동 생성

## 2. Tech Stack

| 항목 | 선택 | 비고 |
|---|---|---|
| Static site generator | Jekyll | Chirpy 호환 |
| Theme | Chirpy (jekyll-theme-chirpy) | chirpy-starter 템플릿 사용 |
| Hosting | GitHub Pages | 무료 |
| Build/Deploy | GitHub Actions | chirpy-starter에 동봉된 워크플로우 |
| Repo | `Jeff-Jins/Jeff-Jins.github.io` | user site 규칙 |
| URL | `https://jeff-jins.github.io` | 기본 도메인 |
| Comments | giscus | GitHub Discussions 기반 |

**Chirpy 선택 이유**
- 기술 블로그 표준. 다크모드/검색(lunr)/TOC/카테고리/태그 기본 내장
- 활발히 유지보수, 한국어 글 사례 풍부
- chirpy-starter 사용 시 push만 해도 GitHub Actions가 빌드/배포

## 3. Site Structure

```
/                  ← 홈: 짧은 인트로(영문) + 최근 글 5개 + "Full Resume →" CTA
/posts/            ← 블로그 글 목록 (페이지네이션, Chirpy 기본)
/categories/       ← 카테고리별 묶음 (Chirpy 기본)
/tags/             ← 태그별 묶음 (Chirpy 기본)
/archives/         ← 연/월별 아카이브 (Chirpy 기본)
/resume/           ← 영문 이력서 (커스텀 탭)
/about/            ← 짧은 자기소개 (Chirpy 기본 About, 영문)
```

**사이드바 네비** (좌측): Home · Categories · Tags · Archives · Resume · About

**홈 페이지 커스텀** (Chirpy 기본 home 레이아웃을 약간 수정)
1. 인사말 1-2줄 + 본인 소개 1단락 (영문)
2. Latest Posts 섹션 (최근 5개 카드 — Chirpy 기본 home 그대로)
3. "📄 Full Resume →" 버튼 (→ `/resume`)

## 4. Content & Language Policy

| 컨텐츠 | 언어 | 위치 |
|---|---|---|
| 이력서 | English | `_tabs/resume.md` |
| About | English | `_tabs/about.md` |
| 블로그 글 | 한국어 위주 (글마다 영어 가능) | `_posts/YYYY-MM-DD-slug.md` |
| 사이트 메타 (`_config.yml`) | English | title, tagline, description |

**초기 카테고리 후보** (실 운영하며 정착): `Frontend`, `Backend`, `AI/ML`, `webOS`, `Infra`, `TIL`

**Post front matter 표준**
```yaml
---
title: "글 제목"
date: 2026-04-30 14:00:00 +0900
categories: [Frontend, React]
tags: [react, rsc]
---
```

## 5. Features

**Chirpy 기본 활용 (별도 작업 없음)**
- Code highlighting · TOC · 다크모드 토글 · 검색(lunr.js) · 카테고리/태그 페이지 · RSS · SEO 메타 · 반응형 레이아웃

**활성화/추가**
- **giscus 댓글**: Repo의 GitHub Discussions에 댓글용 카테고리 생성하여 연결
- **소셜 링크**: GitHub, LinkedIn, Email (`_data/contact.yml`)
- **MathJax**: 기술 글에 수식 가능성 있어 활성화 (Chirpy 옵션 토글)

**보류**
- Analytics
- Custom domain
- Newsletter / RSS-to-email

## 6. Resume Page Design

`_tabs/resume.md` — 영문 마크다운 단일 페이지. 스크롤로 읽기 가능한 단정한 형식.

**섹션 구성**
1. **Header** — 이름, 직함, 위치, 연락처(Email / GitHub / LinkedIn), 한 줄 요약
2. **Summary** — 2-3문장 (focus: webOS, AI agent infrastructure)
3. **Experience** — 회사/역할/기간 + 임팩트 중심 bullet 3-5개 per role
4. **Projects** — 주요 프로젝트 (이름, 한 줄 설명, 사용 기술, 결과물)
5. **Skills** — 카테고리화 (Languages / Frameworks / Infra / Tools)
6. **Education** — 학교, 전공, 기간

**초기 버전 작성 전략**: spec/plan에서는 영문 placeholder 구조만 둔다. 실제 내용은 **사용자가 배포 후 직접 채운다** (자동화 대상 아님).

## 7. Workflow

**글쓰기 흐름**
1. `_posts/YYYY-MM-DD-slug.md` 생성
2. 프론트매터 + 본문 작성
3. (선택) 로컬 미리보기: `bundle exec jekyll serve`
4. `git add && git commit && git push`
5. GitHub Actions 빌드 → 약 1분 후 라이브

**이력서 갱신**
- `_tabs/resume.md` 수정 → push. GitHub 웹 에디터로도 가능.

**로컬 개발 환경 (선택)**
- Ruby 3.x + Bundler 있으면 로컬 미리보기 가능
- 없어도 push 후 빌드 결과로 확인 가능 (피드백 1분 정도 지연)

## 8. Implementation Outline

상세 단계는 별도 implementation plan에서 작성. 큰 흐름만 정리:

1. 환경 점검 — `gh auth status`, `git config user.name/email`, Ruby 유무
2. `Jeff-Jins/Jeff-Jins.github.io` 레포 생성 (chirpy-starter 기반)
3. 로컬 클론 → `_config.yml` 채움 (title, author, social, giscus 자리)
4. `_tabs/about.md` (영문 1단락) 작성
5. `_tabs/resume.md` 신설 (Section 6 구조)
6. 홈 레이아웃 커스텀 (인트로 + Resume CTA)
7. 첫 포스트 1편 (placeholder 또는 실제 글)
8. GitHub Pages 활성화, GitHub Discussions enable, giscus 연결
9. push → GitHub Actions 빌드 모니터링 → 배포 URL 검증

## 9. Decisions Deferred to Implementation

- **giscus용 Discussions 카테고리 이름**: 기본 `General` 사용, 댓글 늘면 전용 카테고리 분리
- **이력서 실제 내용**: spec/plan은 placeholder 구조만 제공. 사용자가 배포 후 본인이 직접 채움 (자동화 대상에서 제외).
- **로컬 Ruby 환경**: 없으면 설치 권장만 하고 스킵. 푸시 빌드로 검증.
- **홈 인트로 문구**: 사용자에게 한 번 확인받고 작성

## 10. Success Criteria

배포 완료 시 모두 충족되어야 함:

- [ ] `https://jeff-jins.github.io` 접속 가능 (HTTP 200)
- [ ] 홈에 인트로 + 최근 글 + Full Resume 링크 노출
- [ ] `/resume` 페이지에 영문 이력서 1차 버전 표시
- [ ] 블로그 글 최소 1편 published
- [ ] giscus 댓글 박스 렌더링 (게시물 하단)
- [ ] 다크모드 토글 동작
- [ ] 사이트 검색 동작
- [ ] 모바일 레이아웃 정상

## 11. Out of Scope (Future)

- Custom domain (`jeffjins.dev` 등)
- PDF 이력서 자동 생성
- 분석/방문자 통계
- 영어 블로그 글 본격 운영
- 시리즈 글 연결 UX 개선
