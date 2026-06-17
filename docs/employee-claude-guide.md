# 채용 인재검색 스킬 통합 직원용 사용 가이드

이 문서는 비개발자 직원도 `korea-talent-search-skills`를 설치하고 Codex 또는 Claude Code에서 잡코리아/사람인 인재검색을 안전하게 수행할 수 있도록 안내한다.

지원 대상:

- Windows 사용자: PowerShell 기준
- macOS 사용자: Terminal 기준

포함 스킬:

```text
jobkorea-talent-search
saramin-talent-search
```

각 스킬의 용도:

- `jobkorea-talent-search`: 잡코리아 기업회원 인재검색에서 마스킹된 이력서 정보를 비교해 유료 열람 전 shortlist 작성
- `saramin-talent-search`: 사람인 기업회원 인재풀에서 마스킹된 후보 정보를 비교해 유료 열람 전 shortlist 작성

## 개발자 전용이 아니다

이 스킬 묶음은 개발자 채용 전용이 아니다. 잡코리아/사람인 인재풀에서 검색할 수 있는 모든 직무에 사용할 수 있다.

예시 직무:

- 영업/세일즈/고객성공
- 마케팅/브랜드/콘텐츠/CRM
- 디자이너/UX/UI/BX
- PM/PO/서비스기획/전략기획
- HR/채용/교육/조직문화
- 재무/회계/세무/IR
- 운영/MD/SCM/물류/구매
- 제조/품질/생산관리
- 법무/총무/사무지원
- 개발/데이터/AI/보안/인프라

중요한 것은 “직무명”이 아니라 아래 조건을 얼마나 구체적으로 쓰는지다.

```text
- 찾는 직무
- 경력 범위
- 지역
- 반드시 필요한 경험
- 있으면 좋은 경험
- 제외할 경력/업종/업무
- 성과를 판단할 기준
- 유료 열람 추천 인원 수
```

## 직무별 평가 기준 예시

| 직무 | 높게 볼 근거 | 낮게 볼 근거 |
| --- | --- | --- |
| 영업/B2B 세일즈 | 매출 실적, 신규 고객 발굴, 계약 협상, CRM, 업종 경험 | 단순 매장 판매, 텔레마케팅만 수행, 성과 근거 없음 |
| 마케팅 | ROAS/CAC/LTV 개선, GA4, 광고 운영, CRM, 캠페인 성과 | 콘텐츠 제작만 있고 성과 지표 없음 |
| 디자인 | 포트폴리오, 브랜드 일관성, Figma/Adobe, 실제 런칭 경험 | 툴 나열만 있고 산출물/역할 불명확 |
| PM/PO/기획 | 문제정의, 지표 개선, 요구사항 정리, 협업, 출시 경험 | 문서 작성만 있고 의사결정/성과 근거 없음 |
| HR/채용 | 다이렉트 소싱, 채용 포지션 수, 면접/오퍼 운영, 노무/평가/보상 | 단순 서류관리만 수행 |
| 재무/회계 | 결산, 세무, 감사 대응, 자금관리, ERP 사용 | 보조 업무 중심, 책임 범위 불명확 |
| 운영/MD/SCM | 재고/매출/마진 개선, 공급사 협상, 물류 지표 개선 | 단순 등록/반복 운영 중심 |
| 개발/데이터/AI | 프로젝트/서비스 운영, 기술스택, GitHub/포트폴리오, 성능/자동화 성과 | 키워드 나열만 있고 구현/운영 근거 없음 |

Codex/Claude에게 요청할 때는 “이 직무에서는 어떤 근거를 높게 볼지”를 같이 적으면 결과 품질이 좋아진다.

## 1. 전체 흐름

처음 사용하는 직원은 아래 순서대로 진행한다.

1. PowerShell 또는 Terminal을 연다.
2. Node.js가 설치되어 있는지 확인한다.
3. 없으면 Node.js LTS를 설치한다.
4. Codex 또는 Claude Code가 설치되어 있는지 확인한다.
5. 없으면 회사 표준 방식으로 Codex 또는 Claude Code를 설치한다.
6. 채용 인재검색 스킬 2개를 한 번에 설치한다.
7. Codex 또는 Claude Code를 실행한다.
8. 에이전트에게 잡코리아/사람인 검색 요청을 한다.
9. 브라우저가 열리면 직원 본인이 직접 로그인/2차 인증을 완료한다.
10. 에이전트가 마스킹된 정보 기준으로 후보 shortlist를 만든다.

## 2. Windows 준비

Windows 사용자는 `PowerShell`을 사용한다.

PowerShell 여는 방법:

1. 키보드에서 `Windows` 키를 누른다.
2. `PowerShell`을 검색한다.
3. `Windows PowerShell` 또는 `Terminal`을 실행한다.

관리자 권한이 꼭 필요한 것은 아니다. 다만 프로그램 설치 명령이 실패하면 회사 PC 권한 정책 때문에 관리자 권한 또는 IT 담당자 도움이 필요할 수 있다.

### 2.1 Node.js 확인

PowerShell에서 아래 명령을 실행한다.

```powershell
node -v
npm -v
```

정상 예시:

```text
v20.11.0
10.2.4
```

`node` 또는 `npm`을 찾을 수 없다는 메시지가 나오면 Node.js를 설치해야 한다.

### 2.2 Windows에 Node.js 설치

방법 A: winget 사용 가능 시

```powershell
winget install OpenJS.NodeJS.LTS
```

설치 후 PowerShell을 완전히 닫고 다시 연 다음 확인한다.

```powershell
node -v
npm -v
```

방법 B: 직접 다운로드

1. 아래 사이트로 이동한다.

```text
https://nodejs.org/
```

2. `LTS` 버전을 다운로드한다.
3. 설치 중 옵션은 기본값으로 둔다.
4. 설치 완료 후 PowerShell을 새로 열고 확인한다.

```powershell
node -v
npm -v
```

### 2.3 Git 확인

public GitHub 저장소 설치는 보통 `npx`만으로 충분하지만, 환경에 따라 Git이 필요할 수 있다.

```powershell
git --version
```

없으면 아래 중 하나로 설치한다.

```powershell
winget install Git.Git
```

또는 직접 다운로드:

```text
https://git-scm.com/download/win
```

설치 후 PowerShell을 새로 연다.

## 3. macOS 준비

macOS 사용자는 `Terminal`을 사용한다.

Node.js 확인:

```bash
node -v
npm -v
```

없으면 Homebrew로 설치한다.

```bash
brew install node
```

Git 확인:

```bash
git --version
```

## 4. Codex 또는 Claude Code 확인

인사 직원 PC에 Codex를 설치했다면 Codex를 우선 사용한다.

Codex 확인:

```bash
codex --version
```

정상적으로 버전이 나오면 Codex용 스킬 설치 단계로 진행한다.

Claude Code를 사용하는 직원은 아래도 확인할 수 있다.

```bash
claude --version
```

Claude Code가 설치되어 있지 않다면 회사 표준 Claude Code 설치 가이드를 따른다. 회사에서 별도 제한이 없다면 아래 방식으로 설치할 수 있다.

```bash
npm install -g @anthropic-ai/claude-code
```

설치 후 새 PowerShell/Terminal을 열고 다시 확인한다.

```bash
claude --version
```

주의:

- 회사 PC에서 전역 npm 설치가 막혀 있을 수 있다.
- 설치가 실패하면 IT 담당자에게 Node.js/npm 전역 패키지 설치 권한을 문의한다.
- Codex/Claude Code 로그인은 개인/회사 계정 정책에 따른다.

## 5. Claude/Codex에게 설치까지 맡기는 방법

Node.js, Git, Codex/Claude Code 설치가 익숙하지 않은 직원은 Codex, Claude Code, 또는 터미널을 다룰 수 있는 다른 LLM 에이전트에게 설치를 맡길 수 있다. 인사 직원 PC에는 Codex를 설치했으므로 아래 프롬프트에서는 Codex 설치/사용을 기본값으로 둔다.

단, 아래 작업은 직원이 직접 승인해야 한다.

```text
- Windows 관리자 권한 승인
- Windows UAC/보안 팝업 승인
- 회사 보안 프로그램 허용
- Codex/Claude 계정 로그인
- 브라우저 로그인
- 잡코리아/사람인 2차 인증
```

에이전트가 자동으로 해도 되는 작업:

```text
- Node.js/npm 설치 여부 확인
- Git 설치 여부 확인
- Codex 또는 Claude Code 설치 여부 확인
- 허용된 설치 명령 실행
- 스킬 설치
- 설치 확인
```

### Windows 자동 설치 요청 프롬프트

아래 문구를 Claude, Codex, 또는 터미널 실행이 가능한 LLM 에이전트에게 그대로 붙여넣는다.

```text
내 Windows PC에 채용 인재검색 스킬을 설치해줘.

목표:
- Node.js/npm이 없으면 설치
- Git이 없으면 설치
- Codex가 설치되어 있는지 확인
- korea-talent-search-skills를 Codex용으로 설치
- 설치 후 두 스킬이 보이는지 확인

설치할 스킬:
https://github.com/ez-edu/korea-talent-search-skills

진행 방식:
1. 먼저 아래 명령으로 현재 설치 상태를 확인해줘.
   - node -v
   - npm -v
   - git --version
   - codex --version

2. Node.js가 없으면 Windows에서 아래 명령으로 설치해줘.
   winget install OpenJS.NodeJS.LTS

3. Git이 없으면 아래 명령으로 설치해줘.
   winget install Git.Git

4. Codex가 없으면 회사에서 안내한 Codex 설치 방식으로 설치하거나, 설치 방법을 나에게 안내해줘.

5. 설치 후 PowerShell을 새로 열어야 하는 경우 나에게 알려줘.

6. 스킬은 아래 명령으로 Codex용 전역 설치해줘.
   npx --yes skills add ez-edu/korea-talent-search-skills --skill "*" --agent codex -g -y

7. 설치 확인은 아래 명령으로 해줘.
   npx --yes skills list --agent codex -g

주의:
- 관리자 권한 승인, Windows 보안 팝업, 로그인, 2차 인증은 내가 직접 처리할게.
- 비밀번호, OTP, 인증번호, 쿠키는 요청하지 마.
- 설치 명령 실행 중 멈추면 어떤 화면에서 멈췄는지 알려줘.
- 회사 보안 정책 때문에 설치가 막히면 우회하지 말고 IT 담당자에게 요청할 내용을 정리해줘.
```

### macOS 자동 설치 요청 프롬프트

```text
내 macOS에 채용 인재검색 스킬을 설치해줘.

목표:
- Node.js/npm이 없으면 설치
- Git이 없으면 설치
- Codex가 설치되어 있는지 확인
- korea-talent-search-skills를 Codex용으로 설치
- 설치 후 두 스킬이 보이는지 확인

설치할 스킬:
https://github.com/ez-edu/korea-talent-search-skills

진행 방식:
1. 먼저 아래 명령으로 현재 설치 상태를 확인해줘.
   - node -v
   - npm -v
   - git --version
   - codex --version

2. Homebrew가 있고 Node.js가 없으면 아래 명령으로 설치해줘.
   brew install node

3. Codex가 없으면 회사에서 안내한 Codex 설치 방식으로 설치하거나, 설치 방법을 나에게 안내해줘.

4. 스킬은 아래 명령으로 Codex용 전역 설치해줘.
   npx --yes skills add ez-edu/korea-talent-search-skills --skill "*" --agent codex -g -y

5. 설치 확인은 아래 명령으로 해줘.
   npx --yes skills list --agent codex -g

주의:
- 관리자 비밀번호, 로그인, 브라우저 권한 승인, 2차 인증은 내가 직접 처리할게.
- 비밀번호, OTP, 인증번호, 쿠키는 요청하지 마.
- 설치 명령 실행 중 멈추면 어떤 화면에서 멈췄는지 알려줘.
- 회사 보안 정책 때문에 설치가 막히면 우회하지 말고 IT 담당자에게 요청할 내용을 정리해줘.
```

### 자동 설치가 막힐 때

아래 상황에서는 LLM 에이전트가 직접 해결하지 못할 수 있다.

```text
- winget이 없거나 회사 정책으로 차단됨
- npm 전역 설치가 차단됨
- 관리자 권한이 없음
- GitHub 접근이 차단됨
- 보안 프로그램이 브라우저 자동 실행을 차단함
- Codex 또는 Claude Code 로그인/계정 인증이 필요함
```

이 경우 에이전트에게 “우회하지 말고 IT 담당자에게 전달할 요청 문구를 정리해줘”라고 요청한다.

IT 담당자에게 전달할 예시:

```text
채용 인재검색용 Codex 스킬 설치를 위해 아래 프로그램/명령 실행 권한이 필요합니다.

필요 프로그램:
- Node.js LTS
- npm/npx
- Git
- Codex

필요 네트워크 접근:
- https://github.com/ez-edu/korea-talent-search-skills
- npm registry 접근

설치/확인 명령:
- node -v
- npm -v
- git --version
- codex --version
- npx --yes skills add ez-edu/korea-talent-search-skills --skill "*" --agent codex -g -y
```

## 6. 스킬 설치

이 저장소는 public이므로 GitHub 권한 없이 설치할 수 있다.

인사 직원 PC에 Codex를 설치했다면 아래 명령을 그대로 복사해 실행한다.

```bash
npx --yes skills add ez-edu/korea-talent-search-skills --skill "*" --agent codex -g -y
```

Claude Code를 사용하는 직원은 아래 명령을 사용한다.

```bash
npx --yes skills add ez-edu/korea-talent-search-skills --skill "*" --agent claude-code -y
```

설치 중 `Proceed?` 같은 질문이 나오면 `y`를 입력한다. 위 명령은 대부분의 확인을 자동으로 처리하도록 `--yes`와 `-y`를 포함한다.

로컬 개발 버전을 설치해야 하는 관리자는 아래 명령을 사용한다.

```bash
npx --yes skills add /Users/wbjung/korea-talent-search-skills --skill "*" --agent codex -g -y
```

Claude Code 로컬 개발 버전은 아래 명령을 사용한다.

```bash
npx --yes skills add /Users/wbjung/korea-talent-search-skills --skill "*" --agent claude-code -y
```

## 7. 설치 확인

Codex 확인:

```bash
npx --yes skills list --agent codex -g
```

Claude Code 확인:

```bash
npx --yes skills list --agent claude-code
```

아래 두 스킬이 보이면 정상이다.

```text
jobkorea-talent-search
saramin-talent-search
```

보이지 않으면 Codex 또는 Claude Code를 종료 후 다시 실행하거나, 설치 명령을 다시 실행한다.

## 8. Codex 또는 Claude Code 실행

Codex 실행:

```bash
codex
```

Claude Code 실행:

```bash
claude
```

에이전트가 실행되면 아래 예시 중 하나를 복사해 입력한다.

## 9. 잡코리아 검색 요청 예시

```text
jobkorea-talent-search 스킬을 사용해서 잡코리아에서 백엔드 엔지니어 후보를 찾아줘.
비로그인 fallback으로 진행하지 말고 먼저 브라우저를 열어 기업회원 로그인 세션을 확인해줘.
로그인이 안 되어 있으면 내가 직접 로그인할 수 있게 안내해줘.

조건:
- 경력: 4~10년
- 지역: 서울 또는 경기 성남
- 필수: Java, Spring Boot, AWS
- 우대: Kafka, Kubernetes, 대용량 트래픽 경험
- 제외: QA 전담, SI 유지보수 위주
- 포트폴리오/GitHub/실서비스 운영 경험이 보이면 높게 평가
- 유료 열람 추천 Top 10

각 후보마다 URL과 검토 수준을 표시해줘.
유료 열람, 마스킹 해제, 포지션 제안, 스크랩, 메모는 하지 마.
```

## 10. 사람인 검색 요청 예시

```text
saramin-talent-search 스킬을 사용해서 사람인 인재풀에서 백엔드 엔지니어 후보를 찾아줘.
먼저 브라우저를 열어 사람인 기업회원 로그인과 2차 인증 완료 여부를 확인해줘.
로그인이나 2차 인증이 필요하면 내가 직접 완료할 수 있게 안내해줘.

조건:
- 경력: 4~10년
- 지역: 서울 또는 경기 성남
- 필수: Java, Spring Boot, AWS
- 우대: Kafka, Kubernetes, 대용량 트래픽 경험
- 제외: QA 전담, SI 유지보수 위주
- 포트폴리오/GitHub/실서비스 운영 경험이 보이면 높게 평가
- 유료 열람 추천 Top 10

목록만 보고 Top 10을 확정하지 말고, 가능한 후보별 상세/프로필 페이지를 열어 현재 보이는 마스킹 상세 정보까지 확인해줘.
각 후보마다 URL과 검토 수준을 표시해줘.
유료 열람, 연락처 확인, 포지션 제안, 스크랩, 메모는 하지 마.
```

## 11. 비개발 직무 검색 요청 예시

### B2B 영업 후보

```text
jobkorea-talent-search 스킬을 사용해서 잡코리아에서 B2B SaaS 영업 후보를 찾아줘.

조건:
- 경력: 3~8년
- 지역: 서울/경기
- 필수: B2B 영업, 신규 고객 발굴, 계약 협상, CRM 사용 경험
- 우대: SaaS, IT 솔루션, 엔터프라이즈 영업, 파이프라인 관리
- 제외: 단순 매장 판매, 텔레마케팅 위주
- 매출 실적, 전환율, 고객관리 경험을 높게 평가
- 유료 열람 추천 Top 10

각 후보마다 URL과 검토 수준을 표시해줘.
유료 열람, 연락처 확인, 제안 발송, 스크랩, 메모는 하지 마.
```

### 퍼포먼스 마케터 후보

```text
saramin-talent-search 스킬을 사용해서 사람인 인재풀에서 퍼포먼스 마케터 후보를 찾아줘.

조건:
- 경력: 3~7년
- 지역: 서울
- 필수: Meta Ads, Google Ads, GA4, 퍼포먼스 마케팅
- 우대: 앱 마케팅, CRM, SQL, 데이터 기반 의사결정
- 제외: 콘텐츠 제작만 담당한 후보
- ROAS, CAC, LTV, 전환율 개선 경험을 높게 평가
- 유료 열람 추천 Top 10

목록만 보고 Top 10을 확정하지 말고, 가능한 후보별 상세/프로필 페이지를 열어 현재 보이는 마스킹 상세 정보까지 확인해줘.
각 후보마다 URL과 검토 수준을 표시해줘.
유료 열람, 연락처 확인, 제안 발송, 스크랩, 메모는 하지 마.
```

### 브랜드/BX 디자이너 후보

```text
saramin-talent-search 스킬을 사용해서 사람인 인재풀에서 BX/브랜드 디자이너 후보를 찾아줘.

조건:
- 경력: 3~8년
- 지역: 서울
- 필수: 브랜드 디자인, 그래픽 디자인, Figma 또는 Adobe
- 우대: 스타트업 브랜딩, 패키지 디자인, 캠페인 디자인, 포트폴리오 링크
- 제외: 단순 편집 디자인만 담당한 후보
- 포트폴리오와 브랜드 일관성 구축 경험을 높게 평가
- 유료 열람 추천 Top 10

각 후보마다 URL과 검토 수준을 표시해줘.
유료 열람, 연락처 확인, 제안 발송, 스크랩, 메모는 하지 마.
```

### HR/채용 담당자 후보

```text
jobkorea-talent-search 스킬을 사용해서 잡코리아에서 채용 담당자 후보를 찾아줘.

조건:
- 경력: 3~7년
- 지역: 서울
- 필수: 채용 운영, 다이렉트 소싱, 면접 프로세스 운영
- 우대: IT/스타트업 채용, ATS 사용, 오퍼/처우협의, 채용 브랜딩
- 제외: 단순 인사총무 보조만 수행한 후보
- 채용 포지션 수, 소싱 채널, 채용 성과를 높게 평가
- 유료 열람 추천 Top 10

각 후보마다 URL과 검토 수준을 표시해줘.
유료 열람, 연락처 확인, 제안 발송, 스크랩, 메모는 하지 마.
```

## 12. 로그인/인증 원칙

- 로그인은 직원이 열린 브라우저에서 직접 한다.
- 2차 인증이 나오면 직원이 직접 완료한다.
- 비밀번호, OTP, SMS/메일 인증번호, 세션 쿠키는 Codex/Claude에게 알려주지 않는다.
- 인증 완료 후 에이전트에게 “로그인했어” 또는 “인증 완료했어”라고 말한다.
- 에이전트는 같은 브라우저 세션에서 검색을 이어간다.

Codex/Claude가 여는 브라우저가 평소 사용하는 Chrome/Edge와 다른 프로필일 수 있다. 이 경우 사이트가 새 기기로 인식해 다시 로그인이나 2차 인증을 요구할 수 있다. 이는 정상이다.

## 13. Windows 브라우저/권한 문제 해결

Windows에서는 macOS처럼 Accessibility 권한을 따로 켜는 방식은 아니지만, 아래 문제가 생길 수 있다.

### 브라우저가 안 열리는 경우

1. 기본 브라우저가 Chrome 또는 Edge인지 확인한다.
2. Codex 또는 Claude Code를 종료 후 다시 실행한다.
3. PowerShell을 닫았다가 다시 연다.
4. 회사 보안 프로그램이 자동 브라우저 실행을 막는다면 IT 담당자에게 문의한다.

### 로그인 상태가 공유되지 않는 경우

Codex/Claude가 연 브라우저가 기존 브라우저와 다른 프로필이면 쿠키가 공유되지 않는다.

해결:

```text
Codex/Claude가 연 브라우저 안에서 잡코리아/사람인 기업회원 로그인을 다시 한다.
```

### 2차 인증이 반복되는 경우

새 브라우저 프로필 또는 새 기기로 인식되면 2차 인증이 반복될 수 있다.

해결:

```text
열린 브라우저에서 직원 본인이 직접 2차 인증을 완료한다.
```

### 명령어 실행이 안 되는 경우

`npx`, `node`, `npm`, `codex`, `claude` 명령을 찾을 수 없으면 설치 후 PowerShell을 새로 열었는지 확인한다.

그래도 안 되면 아래를 확인한다.

```powershell
node -v
npm -v
codex --version
claude --version
```

## 14. macOS 권한 문제 해결

Codex 또는 Claude Code가 브라우저를 열거나 화면을 읽지 못하면, 실행한 터미널/앱에 권한을 준다.

```text
System Settings
→ Privacy & Security
→ Accessibility
→ Screen Recording
→ Automation
```

대상 앱 예:

- Terminal
- iTerm
- Visual Studio Code

권한 변경 후 터미널/Codex/Claude Code/브라우저를 재시작한다.

## 15. 기대 결과

중요:

- 후보별 원문 URL은 필수다.
- URL을 못 찾으면 `URL: 추출 실패`와 이유를 적어야 한다.
- 최종 Top 후보는 가능한 한 후보 상세/프로필 페이지까지 열어 현재 보이는 마스킹 상세 정보를 확인한 뒤 선정해야 한다.
- 상세 페이지를 보지 못하고 목록만 보고 추린 경우, 반드시 `목록 기반 1차 shortlist`라고 표시해야 한다.
- 상세 이력 확인 기반인지 목록 기반인지 반드시 구분한다.

각 스킬은 아래 형태로 결과를 반환해야 한다.

```text
인재 shortlist

검색 조건
- 포지션: ...
- 필수 조건: ...
- 우대 조건: ...
- 제외 조건: ...
- 경력/지역: ...
- 모드: 로그인 완료 브라우저 세션
- 검토 수준: 상세 이력 확인 기반 / 목록 기반 1차 shortlist 중 하나

유료 열람 추천 Top N
1. 후보 A
   - 점수: 88/100
   - 검토 수준: 상세 이력 확인 / 목록만 확인 / 상세 접근 실패 중 하나
   - 근거: ...
   - 보이는 경력/프로젝트: ...
   - 기술스택: ...
   - 포트폴리오/링크 신호: ...
   - 리스크: ...
   - 추천 액션: 인사팀 유료 열람 검토
   - URL: https://www.saramin.co.kr/... 또는 URL: 추출 실패

검색 한계
- 마스킹/현재 표시 정보만 분석했음
- 연락처/실명/비공개 정보는 열람하지 않음
- 유료 액션은 실행하지 않음
```

## 16. 문제 해결 요약

### 스킬이 설치되지 않는 경우

Codex:

```bash
npx --yes skills list --agent codex -g
```

Claude Code:

```bash
npx --yes skills list --agent claude-code
```

없으면 다시 설치한다.

Codex:

```bash
npx --yes skills add ez-edu/korea-talent-search-skills --skill "*" --agent codex -g -y
```

Claude Code:

```bash
npx --yes skills add ez-edu/korea-talent-search-skills --skill "*" --agent claude-code -y
```

### Codex/Claude가 비로그인 정보만 보려는 경우

아래처럼 다시 지시한다.

```text
비로그인 정보로 진행하지 마.
브라우저를 열어서 기업회원 로그인 세션을 확인하고,
로그인된 마스킹 후보 정보를 읽을 수 없으면 결과를 확정하지 말고 원인을 알려줘.
```

### 상세 이력 확인 없이 목록만으로 결과를 주는 경우

아래처럼 다시 지시한다.

```text
목록만 보고 Top 10을 확정하지 마.
가능한 후보별 상세/프로필 페이지를 열어 현재 보이는 마스킹 상세 이력까지 확인한 뒤 다시 평가해줘.
상세 페이지를 못 열면 검토 수준을 목록만 확인 또는 상세 접근 실패로 표시해줘.
```


### 사람인이 자꾸 허용/확인을 요구하거나 진행을 멈추는 경우

Codex/Claude가 안전한 읽기 작업에도 자주 허용을 묻는다면 아래 문구로 다시 지시한다.

```text
사람인 검색에서 다음 작업은 읽기 전용으로 진행해도 돼:
- 사람인 인재풀 검색 페이지 열기
- 검색어/직무/경력/지역 필터 입력
- 검색/적용 버튼 클릭
- 결과 목록 스크롤
- 일반 후보 상세/프로필 링크 열기
- 현재 보이는 마스킹/무료 정보 읽기

다만 아래 작업은 하지 마:
- 유료 열람
- 연락처 확인
- 포지션 제안 발송
- 스크랩/관심후보/메모/상태 변경
- 결제
- 비밀번호/OTP/쿠키 요청 또는 저장

목록만 보고 확정하지 말고, 가능한 후보 상세 페이지까지 읽은 뒤 검토 수준을 표시해줘.
```

이렇게 말해도 브라우저 열기 자체를 못 하면 Codex/Claude Code 실행 환경의 브라우저 권한/도구 문제일 수 있다.

## 17. 보안 및 개인정보 주의사항

반드시 지킬 것:

- 계정 비밀번호를 Codex/Claude에게 말하지 않는다.
- 2FA/OTP/SMS/메일 인증번호를 Codex/Claude에게 말하지 않는다.
- 세션 쿠키를 복사하거나 공유하지 않는다.
- 후보자 연락처/실명/개인정보를 외부 문서나 공개 채널에 저장하지 않는다.
- 유료 열람 여부는 인사 담당자가 직접 판단한다.
- 포지션 제안 발송은 반드시 담당자가 직접 확인 후 진행한다.
