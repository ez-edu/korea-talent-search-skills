# jobkorea-talent-search

Vercel Agent Skill for screening JobKorea talent search results with a corporate logged-in browser session.

이 스킬은 잡코리아 기업회원 로그인 상태에서 유료 열람/마스킹 해제 전에 볼 수 있는 마스킹된 인재 이력서를 검색·비교해, 인사팀이 돈을 써서 열람할 후보만 고르게 돕는다.

GitHub: https://github.com/ez-edu/jobkorea-talent-search

직원 설치 명령:

```bash
npx skills add ez-edu/jobkorea-talent-search --agent claude-code
```

## Structure

```text
jobkorea-talent-search/
  README.md
  SKILL.md
  docs/
    employee-claude-guide.md
  scripts/
    jobkorea_talent_search.py
```

## Employee guide

직원용 Claude Code 설치/사용 가이드는 아래 문서를 본다.

```text
docs/employee-claude-guide.md
```


## Install with Vercel Skills CLI

For Claude Code users:

```bash
npx skills add ez-edu/jobkorea-talent-search --agent claude-code
```

For all supported agents on the machine:

```bash
npx skills add ez-edu/jobkorea-talent-search --all
```

If the repository is private, authenticate GitHub first and verify access:

```bash
gh auth status
git ls-remote https://github.com/ez-edu/jobkorea-talent-search.git
```

## Primary workflow

1. 에이전트가 잡코리아 기업 인재검색 페이지를 브라우저로 연다.
2. 로그인 세션이 없으면 사용자에게 열린 브라우저에서 직접 로그인하라고 안내한다.
3. 사용자가 “로그인했어”라고 알려주면 같은 브라우저 세션에서 인재검색을 수행한다.
4. 유료 열람/마스킹 해제 전에 보이는 마스킹된 이력서 정보만 읽는다.
5. 후보를 점수화하고 shortlist를 만든다.
6. 유료 열람할 후보를 추천하되, 유료 열람/제안/스크랩/메모/연락처 확인은 자동 실행하지 않는다.

## No-login fallback

비로그인 상태에서는 경력 상세/포트폴리오/이력서 본문을 정확히 볼 수 없다. 그래도 rough scouting이 필요할 때만 보조 스크립트를 사용한다.

```bash
python3 scripts/jobkorea_talent_search.py \
  --keyword "React TypeScript" \
  --job-category "AI·개발·데이터" \
  --work-area "서울" \
  --career-min 3 \
  --career-max 8 \
  --limit 20
```

## Safety boundary

Do not automate:

- paid resume unlock
- mask removal
- contact reveal
- proposal sending
- scrap/memo/status changes
- payment
- saving account passwords, session cookies, or personal candidate data
