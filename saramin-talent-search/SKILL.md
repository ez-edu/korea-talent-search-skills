---
name: saramin-talent-search
description: 사람인 기업회원 인재풀 로그인 세션에서 마스킹된 후보 정보를 검색·비교해 유료 열람 전 shortlist를 만듭니다.
---

# saramin-talent-search

사람인 인재풀에서 회사가 원하는 후보를 찾는 Vercel Agent Skill이다. 목표는 기업회원 로그인 및 필요한 경우 첫 기기 2차 인증이 완료된 브라우저 세션에서, 유료 열람/연락처 확인/제안 발송 전에 보이는 마스킹된 후보 정보를 분석해 “돈을 써서 열람할 후보”만 추천하는 것이다.

## When to use

Use this skill when the user asks to:

- 사람인 인재풀에서 우리 회사에 맞는 후보를 찾아달라고 요청한다.
- 사람인 기업회원 로그인 세션에서 후보를 검색하고 shortlist를 만들고 싶어 한다.
- 유료 열람/연락처 확인/제안 발송 전에 마스킹된 후보 정보를 비교하고 싶어 한다.
- 특정 직무, 경력, 지역, 포트폴리오, 프로젝트/성과 경험 조건으로 사람인 인재풀을 검색하려 한다.

This skill is role-agnostic. It is not only for developers. Use it for sales, marketing, design, PM/PO, HR, finance, operations, manufacturing, legal/admin, customer success, data/AI, and other roles. Adapt the scoring rubric to the target role instead of forcing developer-oriented signals such as GitHub or technical stack.

## Core safety boundary

Never spend money or create recruiting side effects automatically.

Allowed:

- 사용자가 직접 로그인/2차 인증을 완료한 사람인 기업회원 브라우저 세션에서 검색 조건 입력
- 현재 화면에 보이는 마스킹된 후보 목록과 마스킹된 이력서/프로필 정보 읽기
- 후보 적합도 분석, 점수화, shortlist 작성
- 유료 열람이 필요해 보이는 후보 추천

Not allowed without explicit user confirmation:

- 유료 이력서 열람
- 연락처 확인/연락처 열람
- 포지션 제안/입사 제안 발송
- 스크랩, 관심후보 등록, 메모, 후보 상태 변경
- 결제 또는 유료 상품 사용
- 사람인 계정/비밀번호/OTP/2차 인증번호/세션 쿠키 저장 또는 요청
- 후보자 개인정보를 장기 저장하거나 대량 수집

If a button or action may consume paid credits, reveal contact information, send a proposal, or mutate account state, stop and ask the user to perform/approve it manually.

## Access path

Primary URL:

```text
https://www.saramin.co.kr/zf_user/memcom/talent-pool/main/search
```

This is an account-bound page. Do not assume no-login access. If a public/no-login page appears, treat it as insufficient for accurate screening.

## Login and first-device verification

People may not know that corporate login or first-device verification is required. When a Saramin talent search request starts, open the talent-pool URL in a browser and check the session state.

If no valid corporate login session exists, or if Saramin shows first-device/2FA verification, pause and ask the user to complete it manually in the opened browser. Resume only after the user confirms the talent-pool search screen is visible.

### Login bootstrap message

If login or verification is required, say this in Korean:

```text
사람인 인재풀 검색은 기업회원 로그인과, 처음 사용하는 브라우저/기기에서는 2차 인증이 필요할 수 있습니다.
제가 브라우저로 사람인 인재풀 검색 페이지를 열어둘게요.
열린 브라우저에서 직접 로그인과 필요한 경우 2차 인증을 완료해 주세요.
비밀번호, 인증번호, 세션 쿠키는 저에게 알려주지 마세요.
인재풀 검색 화면이 보이면 “인증 완료했어”라고 알려주세요.
그 다음 같은 브라우저 세션에서 검색을 이어가겠습니다.
```

Do not request, read, store, or type the user's password, OTP, SMS code, email code, authenticator code, or session cookies.

## Inputs to collect

Before searching, normalize the hiring requirements into this structure:

```yaml
role_title: "채용하려는 직무명"
must_have:
  - 반드시 필요한 경험/스킬/업종
nice_to_have:
  - 있으면 좋은 경험/성과/툴
negative_keywords:
  - 제외할 업무/업종/경력 패턴
career:
  min_years: 0
  max_years: 5
location:
  work_area:
    - 서울
    - 경기
evaluation_signals:
  - 이 직무에서 높게 볼 성과/프로젝트/포트폴리오 근거
company_context:
  team: "채용 팀/조직"
  selling_points:
    - 후보에게 어필할 포인트
limit: 20
```

If the user provides a rough request, make a reasonable first search rather than blocking. Ask follow-up questions only when the role is genuinely ambiguous.

## Logged-in browser workflow

1. Open `https://www.saramin.co.kr/zf_user/memcom/talent-pool/main/search` in a browser when a Saramin talent-search request starts.
2. Check whether the page is in a corporate logged-in state and whether the talent-pool search UI is visible.
   - If logged in and the search UI is visible, continue.
   - If login or first-device 2FA is required, show the Login bootstrap message and wait.
   - After the user says “인증 완료했어” or similar, re-check the same page/session.
   - Do not ask for or store passwords, OTPs, cookies, or session data.
3. Enter/search normalized conditions:
   - 통합검색/키워드: core role and must-have technologies
   - 직무/직종: closest Saramin category for the role
   - 경력: min/max years
   - 지역: preferred work areas
   - 학력/희망연봉/상태/최근 업데이트: use only when relevant and visible
   - 제외 키워드: irrelevant roles, technologies, or employment patterns
4. Collect a candidate pool from the visible result list, usually 20–100 candidates depending on the requested limit.
5. First pass: collect a broad candidate pool from the result list.
6. Second pass is mandatory before finalizing Top N: open the profile/resume detail page for each finalist candidate if doing so does not trigger paid unlock/contact reveal/proposal send.
   - Do not finalize a Top N based on list rows only unless detail pages are inaccessible or paid-walled.
   - If detail pages are inaccessible, state that the shortlist is `목록 기반 1차 shortlist` and lower confidence.
   - If detail pages are accessible, state that the shortlist is `상세 이력 확인 기반 shortlist`.
7. Read currently visible masked/free detail sections only:
   - role title and total experience
   - career timeline and responsibilities
   - project/product descriptions
   - skill keywords
   - portfolio/GitHub/blog/URL signals if visible
   - education/certifications/languages if relevant
   - desired location/salary/job-seeking state if visible
   - recent update/activity signal
8. Do not click paid unlock, contact reveal, proposal send, scrap, interest, memo, or state-change buttons unless the user explicitly instructs and confirms the side effect.
9. Score and rank candidates.
10. Return a shortlist with evidence, paid-unlock recommendation reasoning, source level, and a direct candidate/profile URL for every listed candidate. Do not omit URLs unless the site truly exposes no stable link; if a URL is missing, mark it as `URL: 추출 실패` and explain why.

## Saramin search operation recipe

Use this concrete sequence before asking the user for extra permission or clarification. The user already gave permission to perform read-only search and screening when they invoked this skill.

1. Open the primary talent-pool URL.
2. If login/2FA is required, pause for user login only. Do not ask for credentials.
3. Once the search UI is visible, set filters in this order:
   - Keyword/search box: combine `role_title` with 2–5 strongest must-have keywords. For non-developer roles, use role-specific terms such as `B2B영업`, `퍼포먼스마케팅`, `브랜드디자인`, `채용담당`, `회계`, `MD`, not developer terms.
   - 직무/직종: choose the closest visible Saramin category. If an exact category is not visible, use keyword search first and continue.
   - 경력: set min/max years if a career filter is visible.
   - 지역: set preferred work areas if visible.
   - 최근 업데이트/활동/정렬: prefer recently updated or relevance sorting when visible. If not visible, do not block.
   - 제외 키워드: use only if the UI supports it; otherwise apply exclusions during scoring.
4. Click the normal search/apply button or press Enter in the search box.
5. If a filter is hidden behind an expandable panel, expand it only when it is a read-only search/filter UI. Do not open paid product, contact reveal, proposal, scrap, memo, or account-setting flows.
6. Collect a first-pass list from the results page.
7. Open detail/profile pages for finalist candidates in the same browser session when this is a normal profile/resume link and not a paid unlock/contact/proposal action.
8. If the UI blocks detail access or every detail link is paid-walled, report `목록 기반 1차 shortlist` instead of asking the user to approve paid actions.

Permission prompt guidance:

- Safe to proceed without repeated user confirmation: opening the Saramin search URL, typing search/filter text, pressing search/apply, scrolling result lists, opening normal candidate profile/detail links, reading currently visible masked/free text.
- Must stop and ask/hand off: paid unlock, contact reveal, proposal/send, scrap/interest, memo/status changes, payment, credential/OTP/cookie handling.
- If the host agent asks for permission for every browser action, explain once that the next actions are read-only search/filter/detail-read actions covered by this skill, then continue after approval. Do not ask the user to approve each candidate unless the action has side effects.

## Browser extraction helper snippets

When using a browser-capable agent, after a search result page loads, extract candidate links and visible list text with a conservative DOM scan. Saramin may hide the obvious link text or use nested anchors/buttons, so always capture the nearest href, onclick/data attributes, and current detail-page URL. Adjust selectors after observing the actual page. The final answer must include one direct Saramin URL per recommended candidate whenever any link is available.

```javascript
Array.from(document.querySelectorAll('a[href], button, [onclick], [data-resume-sn], [data-resume-no], [data-mem-idx], [data-href], [data-url]'))
  .map((el) => {
    const rawHref = el.getAttribute('href') || el.getAttribute('data-href') || el.getAttribute('data-url') || '';
    const onclick = el.getAttribute('onclick') || '';
    const data = Object.fromEntries(Array.from(el.attributes || []).map((a) => [a.name, a.value]));
    let url = rawHref ? new URL(rawHref, location.origin).href : '';
    const urlLike = (onclick.match(/https?:\/\/[^'")\s]+|\/[^'")\s]+/g) || [])[0];
    if (!url && urlLike) url = new URL(urlLike, location.origin).href;
    const card = el.closest('li, tr, article, section, .item, .card, .list, .row, [class*=item], [class*=card], [class*=resume], [class*=talent]') || el.parentElement;
    const text = (card?.innerText || el.innerText || '').replace(/\s+/g, ' ').trim();
    const blob = [url, onclick, JSON.stringify(data), text].join(' ');
    const isCandidate = /resume|talent|person|profile|applicant|pool|이력서|후보|인재/i.test(blob);
    return { url, text, isCandidate, data, onclick };
  })
  .filter((item) => item.isCandidate && item.text.length > 20)
  .filter((item, index, arr) => (item.url || item.text) && arr.findIndex((x) => (x.url || x.text) === (item.url || item.text)) === index)
  .slice(0, 100);
```

On a masked profile/resume detail page, capture visible text and paid-action hints:

```javascript
(() => {
  const clone = document.body.cloneNode(true);
  clone.querySelectorAll('script,style,noscript,svg').forEach((el) => el.remove());
  const text = clone.innerText.replace(/\n{3,}/g, '\n\n').trim();
  return {
    url: location.href,
    title: document.title,
    text: text.slice(0, 20000),
    paidActionHints: Array.from(document.querySelectorAll('button,a'))
      .map((el) => el.innerText.trim())
      .filter((t) => /열람|결제|구매|연락|제안|마스킹|스크랩|관심|메모|인재/.test(t))
      .slice(0, 80),
  };
})();
```

If paidActionHints suggests paid unlock/contact reveal/proposal send or account mutation, do not click those controls. Read only currently visible text.

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
  - desired work area, recent activity, job-seeking signal
- Nice-to-have signals: 10
  - bonus stack, industry, language, leadership, startup/enterprise experience
- Risk penalty: -15 max
  - keyword stuffing without project evidence, too senior/junior, unrelated domain, stale profile, unclear ownership

Use labels:

- 85–100: Strong paid-unlock candidate
- 70–84: Worth HR review
- 55–69: Backup / only if pool is weak
- below 55: Do not pay yet

## Output format

Return Korean output in this shape. URL is mandatory for every candidate in `유료 열람 추천 Top N`; if not available, write `URL: 추출 실패` and include the reason under `검색 한계`.

```text
사람인 인재풀 shortlist

검색 조건
- 포지션: ...
- 필수 조건: ...
- 우대 조건: ...
- 제외 조건: ...
- 경력/지역: ...
- 모드: 로그인/인증 완료 브라우저 세션의 마스킹 후보 정보

유료 열람 추천 Top N
1. 후보 A (마스킹 이름 또는 식별 가능한 페이지 정보)
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
- 마스킹/현재 표시 정보만 분석했음
- 연락처/실명/비공개 정보는 열람하지 않음
- 유료 액션은 실행하지 않음
```

## Failure modes

- Login required: open the Saramin talent-pool URL, show the Login bootstrap message, and ask the user to log in manually with a corporate account.
- First-device verification required: Saramin may treat the agent-opened browser/profile as a new device and require 2FA. This is normal. Ask the user to complete verification manually and resume after confirmation.
- Browser/session unavailable: do not silently switch to no-login scraping. Explain that accurate screening requires a browser session the agent can read/control, and ask the user to enable browser/computer-use capability or host-app permissions.
- Paid unlock/contact wall: stop at the wall and do not click. Mark candidate as “needs HR paid review”. If only list data was visible, explicitly label the result as list-based and do not claim detailed resume review.
- Empty/too broad results: relax/tighten keyword, career, region, recent activity filters.
- Bot/rate limit/403: pause, reduce request rate, or switch to manual browser workflow.
- Upstream UI changed: rediscover the search form and visible data flow before updating instructions.

## Notes for maintainers

This skill follows the Vercel Agent Skills format: a directory containing `SKILL.md` with YAML frontmatter `name` and `description`, plus optional supporting docs/scripts.

Unlike JobKorea, this first version intentionally does not include a no-login HTTP helper. The primary value depends on a user-controlled Saramin corporate login session and, on first use, manual 2FA completion.
