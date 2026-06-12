# 사람인 인재풀 스킬 직원용 사용 가이드

이 문서는 회사 구성원이 `saramin-talent-search` Vercel Agent Skill을 설치하고 Claude Code에서 사람인 인재풀 검색을 안전하게 수행하는 방법을 설명한다.

## 1. 이 스킬로 할 수 있는 일

이 스킬은 사람인 기업회원 로그인 및 필요한 경우 첫 기기 2차 인증이 완료된 브라우저 세션에서 유료 열람/연락처 확인/제안 발송 전에 볼 수 있는 마스킹된 후보 정보를 읽고, 회사 채용 조건에 맞는 후보를 점수화해 shortlist를 만든다.

주요 목적:

- 사람인 인재풀 검색 조건 입력 보조
- 마스킹된 후보 목록/프로필/이력서 정보 검토
- 기술스택, 경력, 프로젝트, 포트폴리오 신호 기준 점수화
- 인사팀이 유료 열람할 후보 추천

이 스킬이 자동으로 하지 않는 일:

- 유료 이력서/프로필 열람
- 연락처 확인
- 포지션/입사 제안 발송
- 스크랩/관심후보/메모/후보 상태 변경
- 결제
- 사람인 계정/비밀번호/OTP/2차 인증번호/세션 쿠키 저장

## 2. 준비물

직원 PC에 아래가 필요하다.

- Node.js 18 이상
- Claude Code 설치 및 로그인
- 사람인 기업회원 계정
- 필요한 경우 사람인 2차 인증 수단
- 브라우저 자동화/화면 접근 권한

Node.js 확인:

```bash
node -v
npm -v
```

Claude Code 확인:

```bash
claude --version
```

## 3. 스킬 설치

### 3.1 로컬 개발 버전 설치

아직 GitHub 배포 전이라면 로컬 폴더에서 설치한다.

```bash
npx skills add /Users/wbjung/saramin-talent-search --agent claude-code
```

또는 해당 폴더에서:

```bash
cd /Users/wbjung/saramin-talent-search
npx skills add . --agent claude-code
```

### 3.2 GitHub 배포 후 설치

GitHub repo를 만든 뒤에는 아래 placeholder를 실제 저장소명으로 바꾼다.

```bash
npx skills add <owner>/<repo> --agent claude-code
```

예시:

```bash
npx skills add ez-edu/saramin-talent-search --agent claude-code
```

### 3.3 설치 확인

```bash
npx skills list --agent claude-code
```

목록에 아래 스킬이 보이면 정상이다.

```text
saramin-talent-search
```

## 4. macOS 권한 설정

사람인 로그인된 화면을 Claude Code가 읽거나 브라우저를 조작하려면 macOS 권한이 필요할 수 있다.

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
- 이 경우 Claude Code가 연 브라우저 창에서 사람인 기업회원 로그인을 다시 해야 한다.
- 사람인이 새 브라우저/기기로 판단하면 2차 인증이 나올 수 있다. 이는 정상이다.
- 비밀번호, OTP, SMS/메일 인증번호는 Claude에게 알려주지 말고 사용자가 직접 입력한다.

## 5. Claude Code에서 사용하기

터미널에서 Claude Code를 실행한다.

```bash
claude
```

처음 요청은 아래처럼 한다.

```text
saramin-talent-search 스킬을 사용해서 사람인 인재풀에서 백엔드 엔지니어 후보를 찾아줘.
먼저 브라우저를 열어서 사람인 기업회원 로그인 세션과 인재풀 검색 화면 접근 가능 여부를 확인해줘.
로그인이나 2차 인증이 필요하면 내가 직접 완료할 수 있게 안내해줘.

조건:
- 경력: 4~10년
- 지역: 서울 또는 경기 성남
- 필수: Java, Spring Boot, AWS
- 우대: Kafka, Kubernetes, 대용량 트래픽 경험
- 제외: QA 전담, SI 유지보수 위주
- 포트폴리오/GitHub/실서비스 운영 경험이 보이면 높게 평가
- 유료 열람, 연락처 확인, 포지션 제안, 스크랩, 메모는 하지 마
- 마스킹된 정보만 보고 유료 열람 추천 후보 Top 10을 뽑아줘
```

## 6. 로그인 및 2차 인증 흐름

정상 동작하면 Claude가 사람인 인재풀 검색 페이지를 브라우저로 연다.

```text
https://www.saramin.co.kr/zf_user/memcom/talent-pool/main/search
```

로그인 세션이 없거나 처음 사용하는 브라우저/기기라서 2차 인증이 필요하면 Claude가 다음처럼 안내해야 한다.

```text
사람인 인재풀 검색은 기업회원 로그인과, 처음 사용하는 브라우저/기기에서는 2차 인증이 필요할 수 있습니다.
제가 브라우저로 사람인 인재풀 검색 페이지를 열어둘게요.
열린 브라우저에서 직접 로그인과 필요한 경우 2차 인증을 완료해 주세요.
비밀번호, 인증번호, 세션 쿠키는 저에게 알려주지 마세요.
인재풀 검색 화면이 보이면 “인증 완료했어”라고 알려주세요.
그 다음 같은 브라우저 세션에서 검색을 이어가겠습니다.
```

직원이 해야 할 일:

1. 열린 브라우저에서 사람인 기업회원으로 직접 로그인한다.
2. 2차 인증이 나오면 직접 완료한다.
3. 인재풀 검색 화면이 보이면 Claude에게 아래처럼 말한다.

```text
인증 완료했어. 같은 브라우저 세션에서 계속 진행해줘.
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
saramin-talent-search 스킬로 사람인 인재풀 후보를 찾아줘.

포지션: <포지션명>
경력: <최소>~<최대>년
지역: <선호 지역>
필수 기술: <기술1>, <기술2>, <기술3>
우대 기술: <기술1>, <기술2>
제외 조건: <제외할 키워드/경력 유형>
높게 평가할 신호: <포트폴리오, 운영 경험, 특정 도메인 등>
결과 수: Top <N>

유료 열람/연락처 확인/포지션 제안/스크랩/메모는 하지 말고,
마스킹된 정보만 보고 유료 열람할 후보를 추천해줘.
```

## 8. 검색 요청 예시

### 프론트엔드 엔지니어

```text
saramin-talent-search 스킬을 사용해서 사람인에서 프론트엔드 엔지니어 후보를 찾아줘.

조건:
- 경력: 3~7년
- 지역: 서울
- 필수: React, TypeScript, Next.js
- 우대: 디자인 시스템, 성능 최적화, B2B SaaS 경험
- 제외: 퍼블리싱만 담당, jQuery 위주, 단순 운영 업무 위주
- 포트폴리오/GitHub/실서비스 운영 경험이 보이면 높게 평가
- 유료 열람 추천 Top 10

유료 열람, 연락처 확인, 포지션 제안, 스크랩, 메모는 하지 마.
```

### AI/ML 엔지니어

```text
saramin-talent-search 스킬로 AI/ML 엔지니어 후보를 찾아줘.

조건:
- 경력: 4~10년
- 지역: 서울 또는 원격 가능
- 필수: Python, PyTorch 또는 TensorFlow, LLM/RAG 경험
- 우대: MLOps, Kubernetes, 벡터DB, 검색/추천 시스템
- 제외: 데이터 라벨링/운영만 한 이력, 연구만 하고 제품화 경험이 없는 이력
- 실제 제품 적용/운영 경험을 높게 평가
- 유료 열람 추천 Top 8

마스킹된 정보만 사용하고 유료 액션은 하지 마.
```

### 백엔드 엔지니어

```text
saramin-talent-search 스킬을 사용해서 백엔드 엔지니어 후보를 찾아줘.

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
사람인 인재풀 shortlist

검색 조건
- 포지션: ...
- 필수 조건: ...
- 우대 조건: ...
- 제외 조건: ...
- 경력/지역: ...
- 모드: 로그인/인증 완료 브라우저 세션의 마스킹 후보 정보

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
- 마스킹/현재 표시 정보만 분석했음
- 연락처/실명/비공개 정보는 열람하지 않음
- 유료 액션은 실행하지 않음
```

## 10. 문제 해결

### Claude가 스킬을 모르는 경우

설치 목록 확인:

```bash
npx skills list --agent claude-code
```

없으면 다시 설치:

```bash
npx skills add /Users/wbjung/saramin-talent-search --agent claude-code
```

Claude Code를 재시작한다.

### 사람인이 2차 인증을 요구하는 경우

정상일 수 있다. Claude가 연 브라우저가 평소 사용하는 Chrome과 다른 프로필이면 사람인이 새 기기로 인식할 수 있다.

해결:

```text
열린 브라우저에서 직원 본인이 직접 2차 인증을 완료한다.
```

### Claude가 비로그인/공개 정보만 보려는 경우

Claude에게 아래처럼 다시 지시한다.

```text
비로그인 정보로 진행하지 마.
브라우저를 열어서 사람인 기업회원 로그인과 2차 인증 완료 여부를 확인하고,
인재풀 검색 화면이 보이는 세션에서만 후보 평가를 진행해줘.
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
4. Claude가 별도 브라우저를 열면 그 브라우저 안에서 사람인에 다시 로그인/인증한다.

## 11. 보안 및 개인정보 주의사항

반드시 지킬 것:

- 사람인 비밀번호를 Claude에게 말하지 않는다.
- 2FA/OTP/SMS/메일 인증번호를 Claude에게 말하지 않는다.
- 세션 쿠키를 복사하거나 공유하지 않는다.
- 후보자 연락처/실명/개인정보를 외부 문서나 공개 채널에 저장하지 않는다.
- 유료 열람 여부는 인사 담당자가 직접 판단한다.
- 포지션 제안 발송은 반드시 담당자가 직접 확인 후 진행한다.

권장:

- 결과는 사내 접근 제한 문서에만 저장한다.
- 후보 URL, 마스킹 이름, 평가 근거만 최소한으로 기록한다.
- 불필요한 후보 데이터는 보관하지 않는다.

## 12. 빠른 시작 요약

```bash
# 1. 스킬 설치
npx skills add /Users/wbjung/saramin-talent-search --agent claude-code

# 2. Claude Code 실행
claude
```

Claude에 입력:

```text
saramin-talent-search 스킬을 사용해서 사람인에서 우리 회사에 맞는 후보를 찾아줘.
먼저 브라우저를 열어 사람인 기업회원 로그인과 2차 인증 완료 여부를 확인해줘.
로그인이나 인증이 안 되어 있으면 내가 직접 완료할 수 있게 안내해줘.

포지션: <포지션명>
경력: <연차>
지역: <지역>
필수 기술: <기술>
우대 조건: <우대 조건>
제외 조건: <제외 조건>
결과: 유료 열람 추천 Top 10

유료 열람/연락처 확인/포지션 제안/스크랩/메모는 하지 마.
```
