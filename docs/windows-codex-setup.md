# Windows Codex 설치/문제해결 가이드

이 문서는 Windows 직원 PC에서 `korea-talent-search-skills`를 설치할 때만 필요하다. 스킬 사용 중에는 기본 SKILL.md에 포함하지 않아 토큰을 절약한다.

## 1. PowerShell 열기

1. Windows 키를 누른다.
2. `PowerShell` 또는 `Terminal`을 검색해 실행한다.
3. 설치 명령이 권한 문제로 실패하면 회사 PC 정책상 관리자 권한 또는 IT 도움이 필요할 수 있다.

## 2. 필수 프로그램 확인

```powershell
node -v
npm -v
git --version
codex --version
```

`node`/`npm`이 없으면 Node.js LTS 설치:

```powershell
winget install OpenJS.NodeJS.LTS
```

`git`이 없으면 Git 설치:

```powershell
winget install Git.Git
```

설치 후 PowerShell을 완전히 닫고 다시 연다.

## 3. 스킬 설치

```powershell
npx --yes skills add ez-edu/korea-talent-search-skills --skill "*" --agent codex -g -y
```

설치 확인:

```powershell
npx --yes skills list --agent codex -g
```

아래 두 개가 보이면 성공:

```text
jobkorea-talent-search
saramin-talent-search
```

## 4. Codex에 설치를 맡기는 프롬프트

직원이 직접 명령을 치기 어렵다면 Codex에 아래를 붙여넣는다.

```text
내 Windows PC에 채용 인재검색 스킬을 설치해줘.

설치할 스킬:
https://github.com/ez-edu/korea-talent-search-skills

먼저 확인:
node -v
npm -v
git --version
codex --version

Node.js가 없으면:
winget install OpenJS.NodeJS.LTS

Git이 없으면:
winget install Git.Git

스킬 설치:
npx --yes skills add ez-edu/korea-talent-search-skills --skill "*" --agent codex -g -y

설치 확인:
npx --yes skills list --agent codex -g

중간에 명령 실행 허용, Windows 보안 팝업, 관리자 권한 승인, 브라우저 로그인, 2차 인증이 나오면 내가 직접 처리할게.
비밀번호, OTP, 인증번호, 쿠키는 묻거나 저장하지 마.
회사 보안 정책 때문에 설치가 막히면 우회하지 말고 IT 담당자에게 요청할 내용을 정리해줘.
```

## 5. 자주 막히는 경우

- `npx`를 찾을 수 없음: Node.js 설치 후 PowerShell 재시작.
- `codex`를 찾을 수 없음: Codex 설치 또는 PATH 등록 필요.
- GitHub 접근 실패: 사내 네트워크/보안 정책 확인.
- npm 실행 차단: 보안 프로그램 또는 사내 정책일 수 있으니 IT에 요청.
- 브라우저 로그인 반복: 에이전트가 연 브라우저 프로필에서 직접 로그인/2FA 완료.
- 권한 팝업: 직원이 직접 승인. 비밀번호/OTP는 에이전트에게 공유하지 않음.
