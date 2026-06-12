# 잡코리아 인재검색 스킬 직원용 사용 가이드

이 문서는 회사 구성원이 `jobkorea-talent-search` Vercel Agent Skill을 설치하고 Claude Code에서 잡코리아 인재검색을 안전하게 수행하는 방법을 설명한다.

배포 저장소:

```text
https://github.com/ez-edu/jobkorea-talent-search
```

직원용 기본 설치 명령:

```bash
npx skills add ez-edu/jobkorea-talent-search --agent claude-code
```

## 1. 이 스킬로 할 수 있는 일

이 스킬은 잡코리아 기업회원 로그인 상태에서 유료 열람/마스킹 해제 전에 볼 수 있는 마스킹된 인재 이력서를 읽고, 회사 채용 조건에 맞는 후보를 점수화해 shortlist를 만든다.

주요 목적:

- 잡코리아 인재검색 조건 입력 보조
- 마스킹된 후보 목록/이력서 본문 검토
- 기술스택, 경력, 프로젝트, 포트폴리오 신호 기준 점수화
- 인사팀이 유료 열람할 후보 추천

이 스킬이 자동으로 하지 않는 일:

- 유료 이력서 열람
- 마스킹 해제
- 연락처 확인
- 포지션 제안 발송
- 스크랩/메모/후보 상태 변경
- 결제
- 잡코리아 계정/비밀번호/세션 쿠키 저장

## 2. 준비물

직원 PC에 아래가 필요하다.

- macOS 또는 Windows/Linux 개발 환경
- Node.js 18 이상
- Claude Code 설치 및 로그인
- 잡코리아 기업회원 계정
- 브라우저 자동화/화면 접근 권한

Node.js 확인:

```bash
node -v
npm -v
```

Node.js가 없으면 설치:

```bash
# macOS + Homebrew
brew install node
```

Claude Code가 설치되어 있는지 확인:

```bash
claude --version
```

Claude Code가 없다면 회사 표준 설치 가이드에 따라 설치하거나 Anthropic Claude Code 문서를 참고한다.

## 3. 스킬 설치

### 3.1 GitHub 저장소에서 설치하는 경우

이 스킬은 아래 GitHub 저장소에서 배포한다.

```text
https://github.com/ez-edu/jobkorea-talent-search
```

Claude Code에 설치:

```bash
npx skills add ez-edu/jobkorea-talent-search --agent claude-code
```

저장소가 private이면 먼저 GitHub 인증이 되어 있어야 한다. 설치가 실패하면 아래 중 하나를 확인한다.

```bash
gh auth status
git ls-remote https://github.com/ez-edu/jobkorea-talent-search.git
```

여러 에이전트에 모두 설치하려면:

```bash
npx skills add ez-edu/jobkorea-talent-search --all
```

### 3.2 로컬 폴더에서 설치하는 경우

개발/테스트 중에는 로컬 폴더에서 설치할 수 있다.

```bash
npx skills add /Users/wbjung/jobkorea-talent-search --agent claude-code
```

또는 해당 폴더에서:

```bash
cd /Users/wbjung/jobkorea-talent-search
npx skills add . --agent claude-code
```

### 3.3 설치 확인

```bash
npx skills list --agent claude-code
```

목록에 아래 스킬이 보이면 정상이다.

```text
jobkorea-talent-search
```

## 4. macOS 권한 설정

잡코리아 로그인된 화면을 Claude Code가 읽거나 브라우저를 조작하려면 macOS 권한이 필요할 수 있다.

Claude Code를 실행하는 앱에 권한을 준다.

- Terminal에서 실행하면 `Terminal`
- iTerm에서 실행하면 `iTerm`
- VS Code 터미널에서 실행하면 `Visual Studio Code`

설정 위치:

```text
System Settings
→ Privacy & Security
→ Accessibility
→ Screen Recording
→ Automation
```

권한을 켠 뒤 Claude Code 또는 터미널을 완전히 종료했다가 다시 실행한다.

주의:

- Claude Code가 별도 Chromium/Playwright 브라우저를 열면 평소 Chrome 로그인 상태가 공유되지 않을 수 있다.
- 이 경우 Claude Code가 연 브라우저 창에서 잡코리아 기업회원 로그인을 다시 해야 한다.
- 비밀번호나 2FA 코드는 Claude에게 알려주지 말고, 사용자가 직접 브라우저에 입력한다.

## 5. Claude Code에서 사용하기

터미널에서 Claude Code를 실행한다.

```bash
claude
```

처음 요청은 아래처럼 한다.

```text
jobkorea-talent-search 스킬을 사용해서 잡코리아에서 백엔드 엔지니어 후보를 찾아줘.
비로그인 fallback으로 진행하지 말고, 먼저 브라우저를 열어서 잡코리아 기업회원 로그인 세션을 확인해줘.
로그인이 안 되어 있으면 내가 직접 로그인할 수 있게 브라우저를 띄워줘.

조건:
- 경력: 3~8년
- 지역: 서울 또는 경기 성남
- 필수: Python, FastAPI, AWS
- 우대: Kubernetes, MLOps, SaaS 운영 경험
- 제외: QA 전담, SI 유지보수 위주
- 포트폴리오/GitHub/운영 서비스 경험이 보이면 높게 평가
- 유료 열람, 마스킹 해제, 포지션 제안, 스크랩, 메모는 하지 마
- 마스킹된 정보만 보고 유료 열람 추천 후보 Top 10을 뽑아줘
```

## 6. 로그인 흐름

정상 동작하면 Claude가 잡코리아 기업 인재검색 페이지를 브라우저로 열고 로그인 여부를 확인한다.

로그인 세션이 없으면 Claude가 다음처럼 안내해야 한다.

```text
잡코리아 인재검색은 경력 상세/포트폴리오/마스킹 이력서 확인을 위해 기업회원 로그인이 필요합니다.
제가 브라우저로 잡코리아 기업 인재검색 페이지를 열어둘게요.
열린 브라우저에서 직접 로그인해 주세요. 비밀번호나 인증정보는 저에게 알려주지 마세요.
로그인이 끝나면 “로그인했어”라고 알려주시면, 같은 브라우저 세션에서 검색을 이어가겠습니다.
```

직원이 해야 할 일:

1. 열린 브라우저에서 잡코리아 기업회원으로 직접 로그인한다.
2. 로그인 완료 후 Claude에게 아래처럼 말한다.

```text
로그인했어. 같은 브라우저 세션에서 계속 진행해줘.
```

## 7. 좋은 검색 요청 작성법

검색 품질은 입력 조건에 크게 좌우된다. 아래 정보를 최대한 넣는다.

필수 정보:

- 포지션명
- 경력 범위
- 근무 지역
- 필수 기술
- 제외할 후보 유형

있으면 좋은 정보:

- 우대 기술
- 산업/도메인 경험
- 프로젝트 유형
- 포트폴리오/GitHub/블로그 선호 여부
- 학력/자격증/외국어 조건
- 현재 팀 소개와 후보에게 어필할 포인트

템플릿:

```text
jobkorea-talent-search 스킬로 잡코리아 인재를 찾아줘.

포지션: <포지션명>
경력: <최소>~<최대>년
지역: <선호 지역>
필수 기술: <기술1>, <기술2>, <기술3>
우대 기술: <기술1>, <기술2>
제외 조건: <제외할 키워드/경력 유형>
높게 평가할 신호: <포트폴리오, 운영 경험, 특정 도메인 등>
결과 수: Top <N>

유료 열람/마스킹 해제/포지션 제안/스크랩/메모는 하지 말고,
마스킹된 정보만 보고 유료 열람할 후보를 추천해줘.
```

## 8. 검색 요청 예시

### 프론트엔드 엔지니어

```text
jobkorea-talent-search 스킬을 사용해서 잡코리아에서 프론트엔드 엔지니어 후보를 찾아줘.

조건:
- 경력: 3~7년
- 지역: 서울
- 필수: React, TypeScript, Next.js
- 우대: 디자인 시스템, 성능 최적화, B2B SaaS 경험
- 제외: 퍼블리싱만 담당, jQuery 위주, 단순 운영 업무 위주
- 포트폴리오/GitHub/실서비스 운영 경험이 보이면 높게 평가
- 유료 열람 추천 Top 10

유료 열람, 마스킹 해제, 포지션 제안, 스크랩, 메모는 하지 마.
```

### AI/ML 엔지니어

```text
jobkorea-talent-search 스킬로 AI/ML 엔지니어 후보를 찾아줘.

조건:
- 경력: 4~10년
- 지역: 서울 또는 원격 가능
- 필수: Python, PyTorch 또는 TensorFlow, LLM/RAG 경험
- 우대: MLOps, Kubernetes, 벡터DB, 검색/추천 시스템
- 제외: 데이터 라벨링/운영만 한 이력, 연구만 하고 제품화 경험이 없는 이력
- 논문보다 실제 제품 적용/운영 경험을 높게 평가
- 유료 열람 추천 Top 8

마스킹된 정보만 사용하고 유료 액션은 하지 마.
```

### 백엔드 엔지니어

```text
jobkorea-talent-search 스킬을 사용해서 백엔드 엔지니어 후보를 찾아줘.

조건:
- 경력: 5~12년
- 지역: 서울/성남
- 필수: Java 또는 Kotlin, Spring Boot, MySQL, Redis
- 우대: 대용량 트래픽, Kafka, MSA, AWS
- 제외: 사내 시스템 유지보수만 오래 한 후보, PM 역할만 한 후보
- 실서비스 장애 대응/성능 개선/아키텍처 설계 경험을 높게 평가
- 유료 열람 추천 Top 10

연락처 확인이나 포지션 제안은 하지 말고 후보 평가표만 만들어줘.
```

## 9. 기대 결과 형식

Claude의 결과는 대략 아래 형태여야 한다.

```text
잡코리아 인재 shortlist

검색 조건
- 포지션: ...
- 필수 조건: ...
- 우대 조건: ...
- 제외 조건: ...
- 경력/지역: ...
- 모드: 로그인 마스킹 이력서

유료 열람 추천 Top N
1. 후보 A
   - 점수: 88/100 — Strong paid-unlock candidate
   - 근거: ...
   - 보이는 경력/프로젝트: ...
   - 기술스택: ...
   - 포트폴리오/링크 신호: ...
   - 리스크: ...
   - 추천 액션: 인사팀 유료 열람 검토
   - URL: ...

보류 후보
- ...

검색 한계
- 마스킹된 정보만 분석했음
- 연락처/실명/비공개 정보는 열람하지 않음
- 유료 액션은 실행하지 않음
```

## 10. 결과를 해석하는 법

점수 기준:

- 85~100: 유료 열람 강력 추천
- 70~84: 인사팀 검토 가치 있음
- 55~69: 후보 풀이 약할 때만 보류 검토
- 55 미만: 유료 열람 비추천

항상 확인할 것:

- 점수보다 근거가 더 중요하다.
- “기술 키워드만 있음”과 “프로젝트에서 실제 사용함”은 다르다.
- 포트폴리오/GitHub/운영 경험이 보이는 후보를 우선 검토한다.
- 마스킹된 정보 기반이므로 최종 판단 전 유료 열람 후 재확인이 필요하다.

## 11. 문제 해결

### 설치 후 Claude가 스킬을 모르는 경우

설치 목록 확인:

```bash
npx skills list --agent claude-code
```

없으면 다시 설치:

```bash
npx skills add ez-edu/jobkorea-talent-search --agent claude-code
```

Claude Code를 재시작한다.

```bash
# Claude Code 종료 후 재실행
claude
```

### Claude가 비로그인 목록만 가져오는 경우

Claude에게 아래처럼 다시 지시한다.

```text
비로그인 fallback으로 진행하지 마.
브라우저를 열어서 잡코리아 기업회원 로그인 세션을 확인하고,
로그인이 없으면 내가 직접 로그인할 수 있게 안내해줘.
로그인된 마스킹 이력서 본문을 읽을 수 없으면 검색 결과를 확정하지 말고 원인을 알려줘.
```

### macOS/Chrome 권한 문제가 나오는 경우

1. Claude Code를 실행한 앱을 확인한다.
   - Terminal
   - iTerm
   - Visual Studio Code
2. 아래 권한을 켠다.

```text
System Settings
→ Privacy & Security
→ Accessibility
→ Screen Recording
→ Automation
```

3. 터미널/Claude Code/브라우저를 종료 후 다시 실행한다.
4. Claude가 별도 브라우저를 열면 그 브라우저 안에서 잡코리아에 다시 로그인한다.

### 브라우저는 열렸지만 로그인 상태가 공유되지 않는 경우

정상일 수 있다. Claude가 평소 Chrome이 아니라 별도 Chromium 프로필을 쓰면 쿠키가 공유되지 않는다.

해결:

```text
Claude가 연 브라우저 창에서 잡코리아 기업회원 로그인을 직접 한다.
```

### 유료 열람 버튼이 보이는 경우

Claude가 클릭하면 안 된다. 직원이 직접 판단해야 한다.

Claude에게 이렇게 말한다.

```text
유료 열람 버튼은 누르지 말고, 현재 보이는 마스킹 정보만 기준으로 후보를 평가해줘.
```

## 12. 보안 및 개인정보 주의사항

반드시 지킬 것:

- 잡코리아 비밀번호를 Claude에게 말하지 않는다.
- 2FA/인증번호를 Claude에게 말하지 않는다.
- 세션 쿠키를 복사하거나 공유하지 않는다.
- 후보자 연락처/실명/개인정보를 외부 문서나 공개 채널에 저장하지 않는다.
- 유료 열람 여부는 인사 담당자가 직접 판단한다.
- 포지션 제안 발송은 반드시 담당자가 직접 확인 후 진행한다.

권장:

- 결과는 사내 접근 제한 문서에만 저장한다.
- 후보 URL, 마스킹 이름, 평가 근거만 최소한으로 기록한다.
- 불필요한 후보 데이터는 보관하지 않는다.

## 13. 관리자용 업데이트 방법

스킬 저장소가 업데이트되면 직원은 아래 명령으로 갱신한다.

```bash
npx skills update jobkorea-talent-search
```

또는 재설치:

```bash
npx skills add ez-edu/jobkorea-talent-search --agent claude-code
```

로컬 개발 버전은:

```bash
cd /Users/wbjung/jobkorea-talent-search
npx skills add . --agent claude-code
```

## 14. 빠른 시작 요약

직원이 처음부터 끝까지 실행할 최소 절차:

```bash
# 1. 스킬 설치
npx skills add ez-edu/jobkorea-talent-search --agent claude-code

# 2. Claude Code 실행
claude
```

Claude에 입력:

```text
jobkorea-talent-search 스킬을 사용해서 잡코리아에서 우리 회사에 맞는 후보를 찾아줘.
비로그인 fallback으로 진행하지 말고 먼저 브라우저를 열어 기업회원 로그인 세션을 확인해줘.
로그인이 안 되어 있으면 내가 직접 로그인할 수 있게 안내해줘.

포지션: <포지션명>
경력: <연차>
지역: <지역>
필수 기술: <기술>
우대 조건: <우대 조건>
제외 조건: <제외 조건>
결과: 유료 열람 추천 Top 10

유료 열람/마스킹 해제/포지션 제안/스크랩/메모는 하지 마.
```

브라우저가 열리면 직접 로그인하고 Claude에 입력:

```text
로그인했어. 같은 브라우저 세션에서 계속 진행해줘.
```
