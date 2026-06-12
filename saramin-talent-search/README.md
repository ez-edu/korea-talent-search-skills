# saramin-talent-search

Vercel Agent Skill for screening Saramin talent-pool candidates with a corporate logged-in browser session.

이 스킬은 사람인 기업회원 로그인 및 필요한 경우 첫 기기 2차 인증이 완료된 브라우저 세션에서 유료 열람/연락처 확인/제안 발송 전에 볼 수 있는 마스킹된 후보 정보를 검색·비교해, 인사팀이 돈을 써서 열람할 후보만 고르게 돕는다.

## Structure

```text
saramin-talent-search/
  README.md
  SKILL.md
  docs/
    employee-claude-guide.md
```

## Employee guide

직원용 Claude Code 설치/사용 가이드는 아래 문서를 본다.

```text
docs/employee-claude-guide.md
```

## Install with Vercel Skills CLI

로컬 개발 중에는:

```bash
npx skills add /Users/wbjung/saramin-talent-search --agent claude-code
```

GitHub에 배포한 뒤에는:

```bash
npx skills add <owner>/<repo> --agent claude-code
```

## Primary workflow

1. 에이전트가 사람인 인재풀 검색 페이지를 브라우저로 연다.
2. 기업회원 로그인 세션이 없거나 첫 기기 2차 인증이 필요하면 사용자에게 직접 완료하라고 안내한다.
3. 사용자가 “인증 완료했어”라고 알려주면 같은 브라우저 세션에서 인재풀 검색을 수행한다.
4. 유료 열람/연락처 확인/제안 발송 전에 보이는 마스킹된 후보 정보만 읽는다.
5. 후보를 점수화하고 shortlist를 만든다.
6. 유료 열람할 후보를 추천하되, 유료 열람/연락처 확인/제안/스크랩/메모는 자동 실행하지 않는다.

## Safety boundary

Do not automate:

- paid resume/profile unlock
- contact reveal
- proposal sending
- scrap/interest/memo/status changes
- payment
- saving account passwords, OTP codes, session cookies, or personal candidate data
