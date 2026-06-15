# Public release checklist

이 저장소를 누구나 설치할 수 있게 배포하려면 GitHub 저장소를 public으로 전환한다.

## 권장 배포 방식

현재 저장소를 그대로 public으로 전환해도 된다. 저장소에는 계정 비밀번호, OTP, 세션 쿠키, 후보자 개인정보, 유료 상품 정보가 들어 있지 않아야 한다.

공개 설치 명령:

```bash
npx --yes skills add ez-edu/korea-talent-search-skills --skill "*" --agent claude-code -y
```

설치 확인:

```bash
npx skills list --agent claude-code
```

## GitHub에서 public 전환

1. GitHub 저장소로 이동한다.

```text
https://github.com/ez-edu/korea-talent-search-skills
```

2. `Settings` → `General` → `Danger Zone`으로 이동한다.
3. `Change repository visibility`를 선택한다.
4. `Make public`을 선택한다.
5. GitHub가 요구하는 확인 문구를 입력한다.

## 공개 전 확인할 것

아래 값이 저장소에 포함되어 있으면 안 된다.

- 잡코리아/사람인 계정 ID, 비밀번호
- OTP, SMS/메일 인증번호
- 세션 쿠키, localStorage/sessionStorage dump
- 유료 상품/결제 정보
- 후보자의 실명, 연락처, 이메일, 전화번호
- 실제 검색 결과 전문 또는 후보자 개인정보가 포함된 로그

현재 스킬은 사용자가 열린 브라우저에서 직접 로그인/2차 인증을 완료하고, 에이전트는 현재 보이는 마스킹/무료 정보를 읽는 방식으로 설계되어 있다.

## README 명령 업데이트

public 전환 후 README의 설치 명령은 아래처럼 실제 GitHub slug를 사용한다.

```bash
npx --yes skills add ez-edu/korea-talent-search-skills --skill "*" --agent claude-code -y
```

## 기존 private repo를 public으로 만들기 싫은 경우

별도 public repo를 만든다.

```bash
git clone https://github.com/ez-edu/korea-talent-search-skills.git korea-talent-search-skills-public
cd korea-talent-search-skills-public
# 민감하거나 사내 전용인 문구가 있다면 정리
git remote set-url origin https://github.com/ez-edu/korea-talent-search-skills-public.git
git push -u origin main
```

직원/외부 사용자 설치 명령은 public repo slug로 바꾼다.

```bash
npx --yes skills add ez-edu/korea-talent-search-skills-public --skill "*" --agent claude-code -y
```
