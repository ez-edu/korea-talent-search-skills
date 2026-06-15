---
name: jobkorea-talent-search
description: 잡코리아 기업회원 로그인 세션으로 유료 열람 전 마스킹된 인재 이력서를 검색·비교해 인사팀용 shortlist를 만듭니다.
---

# jobkorea-talent-search

잡코리아 기업 인재검색에서 회사가 원하는 인재를 찾는 Vercel Agent Skill이다. 목표는 유료 마스킹 해제/포지션 제안 전에, 기업회원 로그인 상태에서 볼 수 있는 마스킹된 이력서와 공개 목록 정보를 최대한 잘 읽고 비교하여 “돈을 써서 열람할 후보”만 추천하는 것이다.

## When to use

Use this skill when the user asks to:

- 잡코리아에서 우리 회사에 맞는 인재를 찾아달라고 요청한다.
- 잡코리아 기업계정으로 로그인해 마스킹된 이력서만 보고 후보를 추려달라고 요청한다.
- 유료 열람/마스킹 해제 전에 후보를 비교·점수화하고 싶어 한다.
- 특정 직무, 기술스택, 경력, 지역, 포트폴리오, 프로젝트 경험 조건으로 잡코리아 인재검색을 수행하려 한다.

## Core safety boundary

Never spend money or create recruiting side effects automatically.

Allowed:

- 사용자가 직접 로그인한 잡코리아 기업회원 브라우저 세션에서 검색 조건 입력
- 마스킹된 목록과 마스킹된 이력서 본문 읽기
- 후보 적합도 분석, 점수화, shortlist 작성
- 유료 열람이 필요해 보이는 후보 추천

Not allowed without explicit user confirmation:

- 유료 이력서 열람 / 마스킹 해제
- 포지션 제안 발송
- 스크랩, 메모 저장, 후보 상태 변경
- 연락처 수집 또는 외부 저장
- 잡코리아 계정/비밀번호/세션 쿠키를 저장소나 로그에 기록
- 후보자 데이터를 장기 DB화하거나 대량 수집

If a button or action may consume paid credits, reveal contact information, send a proposal, or mutate account state, stop and ask the user to perform/approve it manually.

## Required mode selection

Prefer mode A for the user’s stated goal.

### Mode A — logged-in browser mode, preferred

Use when the user has a JobKorea corporate account. The user may not know that login is required before using the skill. Therefore, when a search request comes in, start by opening the JobKorea corporate talent-search page in a browser and checking the session state. If no valid corporate login session exists, pause the search and instruct the user to log in manually in that browser window/tab. After the user confirms login is complete, continue the search in the same browser session. Never ask for the password and never store cookies.

### Login bootstrap message

If no logged-in corporate session is detected, say this in Korean:

```text
잡코리아 인재검색은 경력 상세/포트폴리오/마스킹 이력서 확인을 위해 기업회원 로그인이 필요합니다.
제가 브라우저로 잡코리아 기업 인재검색 페이지를 열어둘게요.
열린 브라우저에서 직접 로그인해 주세요. 비밀번호나 인증정보는 저에게 알려주지 마세요.
로그인이 끝나면 “로그인했어”라고 알려주시면, 같은 브라우저 세션에서 검색을 이어가겠습니다.
```

Then open `https://www.jobkorea.co.kr/corp/person/find`, wait for the user to log in, and resume from the same page. Do not fall back to no-login mode unless the user explicitly says they cannot or do not want to log in.

### Mode B — no-login public list fallback

Use only for rough scouting when login is unavailable. This mode can search public/masked list summaries, but it cannot reliably inspect career details, portfolio, project content, or full resume sections. Clearly label results as low-confidence.

Helper script for mode B:

```bash
python3 jobkorea-talent-search/scripts/jobkorea_talent_search.py \
  --keyword "React TypeScript" \
  --job-category "AI·개발·데이터" \
  --work-area "서울" \
  --career-min 3 \
  --career-max 8 \
  --limit 20
```

## Inputs to collect

Before searching, normalize the hiring requirements into this structure:

```yaml
role_title: "백엔드 엔지니어"
must_have:
  - Python
  - FastAPI
  - AWS
nice_to_have:
  - Kubernetes
  - MLOps
  - startup experience
negative_keywords:
  - QA only
  - SI only
career:
  min_years: 3
  max_years: 8
location:
  work_area:
    - 서울
    - 경기 성남
  remote_or_hybrid: optional
portfolio_signals:
  - GitHub
  - production service
  - traffic/scale metrics
company_context:
  team: "AI SaaS startup"
  selling_points:
    - small team ownership
    - global product
limit: 20
```

If the user only provides a rough request, make a reasonable first search rather than blocking. Ask follow-up questions only when the role is genuinely ambiguous.

## Logged-in browser workflow

1. Open `https://www.jobkorea.co.kr/corp/person/find` in a browser immediately when a JobKorea talent-search request starts.
2. Check whether the page is in a corporate logged-in state.
   - If logged in, continue with the search.
   - If not logged in, show the Login bootstrap message above and keep the browser/page available for the user.
   - Wait for the user to say login is complete, then re-check the same page/session.
   - Do not ask for or store the password, one-time code, cookies, or session data.
3. Enter/search the normalized conditions:
   - 통합검색: core role and must-have technologies
   - AND keywords: strict must-have terms
   - OR keywords: nice-to-have terms
   - 제외 keywords: irrelevant/screen-out terms
   - 직무/스킬: select the closest JobKorea category, commonly `AI·개발·데이터` for engineering roles
   - 지역: preferred work areas
   - 경력: min/max years
   - 업데이트/최근 활동: prefer recent activity when too many results appear
4. Collect a broad candidate pool from the result list, usually 20–100 candidates depending on user’s requested limit.
5. For each promising candidate, open the resume detail page in a new tab only if it does not trigger paid unlock.
6. Read masked/free sections only:
   - career timeline and total years
   - project/responsibility descriptions
   - skill keywords
   - education/certifications/languages
   - portfolio/GitHub/blog URLs if visible
   - desired location/salary if visible
   - recent activity/update signal
7. Do not click paid unlock, contact reveal, proposal send, scrap, or memo buttons unless the user explicitly instructs and confirms the side effect.
8. Score and rank candidates.
9. Return a shortlist with evidence and “paid unlock recommendation” reasoning.

## Browser extraction helper snippets

When using a browser-capable agent, after a search result page loads, run this in the page console to extract candidate links and visible list text:

```javascript
Array.from(document.querySelectorAll('a[href*="/corp/person/find/resume/view?rNo="]'))
  .map((a) => {
    const card = a.closest('.booth, tr, li, .personList, .list, .row') || a.parentElement;
    const text = (card?.innerText || a.innerText || '')
      .replace(/\s+/g, ' ')
      .trim();
    return {
      url: new URL(a.getAttribute('href'), location.origin).href,
      rNo: new URL(a.getAttribute('href'), location.origin).searchParams.get('rNo'),
      text,
    };
  })
  .filter((item, index, arr) => item.rNo && arr.findIndex((x) => x.rNo === item.rNo) === index)
  .slice(0, 100);
```

On a masked resume detail page, run this to capture only visible text and avoid hidden/script noise:

```javascript
(() => {
  const blocked = ['script', 'style', 'noscript', 'svg'];
  const clone = document.body.cloneNode(true);
  clone.querySelectorAll(blocked.join(',')).forEach((el) => el.remove());
  const text = clone.innerText.replace(/\n{3,}/g, '\n\n').trim();
  return {
    url: location.href,
    title: document.title,
    text: text.slice(0, 20000),
    paidActionHints: Array.from(document.querySelectorAll('button,a'))
      .map((el) => el.innerText.trim())
      .filter((t) => /열람|결제|구매|제안|연락|마스킹|스크랩|메모/.test(t))
      .slice(0, 50),
  };
})();
```

If `paidActionHints` suggests paid unlock/contact reveal/proposal send, do not click those controls. Read only currently visible text.

## Scoring rubric

Score each candidate out of 100. Adjust weights if the user provides a different priority.

### Role-adaptive scoring notes

Do not use developer-only criteria for every role. Adapt evidence to the target job:

- Sales/customer success: revenue, pipeline, outbound/inbound, contract negotiation, CRM, industry/customer segment fit.
- Marketing: ROAS/CAC/LTV, ad channels, GA4/CRM, campaign ownership, funnel/conversion improvement.
- Design: portfolio, launched work, brand consistency, Figma/Adobe, UX process, collaboration with product/marketing.
- PM/PO/planning: problem definition, roadmap, requirements, stakeholder alignment, metric improvement, launch ownership.
- HR/recruiting: direct sourcing, hiring volume, interview process, offer/compensation, ATS, employer branding.
- Finance/accounting: closing, tax, audit, cashflow, ERP, reporting, responsibility scope.
- Operations/MD/SCM: sales/margin/inventory improvement, vendor negotiation, fulfillment/logistics KPIs, process improvement.
- Developer/data/AI: implementation/project evidence, production operation, relevant stack, portfolio/GitHub/blog, scale/automation impact.


- Must-have fit: 35
  - direct evidence of required skills, stack, or domain
- Relevant career depth: 20
  - total years and role seniority match the requested range
- Project/portfolio evidence: 20
  - concrete project descriptions, production impact, portfolio/GitHub/blog, measurable outcomes
- Location/availability fit: 10
  - desired work area, recent activity, job-seeking status
- Nice-to-have signals: 10
  - bonus stack, industry, language, leadership, startup/enterprise experience
- Risk penalty: -15 max
  - keyword stuffing without project evidence, too senior/junior, unrelated domain, stale resume, unclear role ownership

Use labels:

- 85–100: Strong paid-unlock candidate
- 70–84: Worth HR review
- 55–69: Backup / only if pool is weak
- below 55: Do not pay yet

## Output format

Return Korean output in this shape:

```text
잡코리아 인재 shortlist

검색 조건
- 포지션: ...
- 필수 조건: ...
- 우대 조건: ...
- 제외 조건: ...
- 경력/지역: ...
- 모드: 로그인 마스킹 이력서 / 비로그인 목록 fallback

유료 열람 추천 Top N
1. 후보 A (마스킹 이름 또는 rNo)
   - 점수: 88/100 — Strong paid-unlock candidate
   - 근거: ...
   - 보이는 경력/프로젝트: ...
   - 기술스택: ...
   - 포트폴리오/링크 신호: ...
   - 리스크: ...
   - 추천 액션: 인사팀이 유료 열람 검토
   - URL: ...

보류 후보
- ...

검색 한계
- 마스킹 정보만 분석했음
- 연락처/실명/비공개 정보는 열람하지 않음
- 유료 액션은 실행하지 않음
```

## No-login fallback workflow

If corporate login is not available:

1. Use the helper script to fetch public/masked result-list summaries.
2. Score at lower confidence because career/portfolio/detail sections are unavailable.
3. Tell the user that accurate filtering requires logged-in masked resume inspection.
4. Recommend logging in before spending paid unlock credits.

## Failure modes

- Login required: open the JobKorea corporate talent-search page in a browser, show the Login bootstrap message, and ask the user to log in manually with a corporate account.
- Browser or Chrome permission unavailable: do not silently switch to no-login fallback. Explain that accurate screening requires a browser session that the agent can read/control, and ask the user to enable the agent's browser/computer-use capability or macOS permissions. If the user launched the agent from Terminal/iTerm/VS Code, they may need to grant Accessibility, Screen Recording, and Automation permissions to that host app. If the agent uses an isolated Playwright/Chromium browser, the user must log in inside the browser window opened by the agent; a normal Chrome login may not be shared.
- Paid unlock wall: stop at the wall and do not click. Mark candidate as “needs HR paid review”.
- Empty/too broad results: relax/tighten keyword, career, region, recent activity filters.
- Bot/rate limit/403: pause, reduce request rate, or switch to manual browser workflow.
- Upstream UI changed: rediscover the search form and visible AJAX/data flow before editing scripts.

## Notes for maintainers

This skill follows the Vercel Agent Skills format: a directory containing `SKILL.md` with YAML frontmatter `name` and `description`, plus optional supporting scripts. It can be installed with the skills CLI from a repo or specific skill path, e.g.:

```bash
npx skills add <owner/repo> --skill jobkorea-talent-search
```

The included Python helper is only the no-login fallback. The primary workflow is browser/login based because accurate JobKorea resume screening requires corporate login visibility before paid unlock.
