# korea-talent-search-skills

잡코리아와 사람인 인재검색을 한 번에 설치하기 위한 Vercel Agent Skills 묶음 저장소다.

포함 스킬:

```text
jobkorea-talent-search
saramin-talent-search
```

## 직원 설치 명령

Claude Code에 두 스킬을 한 번에 설치:

```bash
npx skills add <owner>/korea-talent-search-skills --skill '*' --agent claude-code -y
```

로컬 개발 버전 설치:

```bash
npx skills add /Users/wbjung/korea-talent-search-skills --skill '*' --agent claude-code -y
```

설치 확인:

```bash
npx skills list --agent claude-code
```

아래 두 스킬이 보이면 정상이다.

```text
jobkorea-talent-search
saramin-talent-search
```

## 사용 예시

잡코리아:

```text
jobkorea-talent-search 스킬을 사용해서 잡코리아에서 백엔드 엔지니어 후보를 찾아줘.
비로그인 fallback으로 진행하지 말고 먼저 브라우저를 열어 기업회원 로그인 세션을 확인해줘.
유료 열람/마스킹 해제/포지션 제안/스크랩/메모는 하지 마.
```

사람인:

```text
saramin-talent-search 스킬을 사용해서 사람인 인재풀에서 백엔드 엔지니어 후보를 찾아줘.
먼저 브라우저를 열어 사람인 기업회원 로그인과 2차 인증 완료 여부를 확인해줘.
유료 열람/연락처 확인/포지션 제안/스크랩/메모는 하지 마.
```

## 직원 가이드

통합 설치/사용 가이드:

```text
docs/employee-claude-guide.md
```

각 스킬별 상세 가이드:

```text
jobkorea-talent-search/docs/employee-claude-guide.md
saramin-talent-search/docs/employee-claude-guide.md
```

## 보안 원칙

두 스킬 모두 아래 액션을 자동화하지 않는다.

- 유료 이력서/프로필 열람
- 마스킹 해제 또는 연락처 확인
- 포지션/입사 제안 발송
- 스크랩/관심후보/메모/후보 상태 변경
- 결제
- 계정 비밀번호, OTP, 인증번호, 세션 쿠키 저장

로그인과 2차 인증은 사용자가 열린 브라우저에서 직접 완료한다.
