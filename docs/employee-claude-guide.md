# 채용 인재검색 스킬 통합 직원용 사용 가이드

이 문서는 `korea-talent-search-skills` 저장소로 잡코리아/사람인 인재검색 스킬을 한 번에 설치하고 Claude Code에서 사용하는 방법을 설명한다.

## 1. 포함 스킬

```text
jobkorea-talent-search
saramin-talent-search
```

각 스킬의 용도:

- `jobkorea-talent-search`: 잡코리아 기업회원 인재검색에서 마스킹된 이력서 정보를 비교해 유료 열람 전 shortlist 작성
- `saramin-talent-search`: 사람인 기업회원 인재풀에서 마스킹된 후보 정보를 비교해 유료 열람 전 shortlist 작성

## 2. 설치

### GitHub 배포 버전

저장소 배포 후 아래 `<owner>`를 실제 GitHub 조직/계정으로 바꿔 실행한다.

```bash
npx skills add <owner>/korea-talent-search-skills --skill '*' --agent claude-code -y
```

예상 예시:

```bash
npx skills add ez-edu/korea-talent-search-skills --skill '*' --agent claude-code -y
```

저장소가 private이면 먼저 GitHub 인증을 확인한다.

```bash
gh auth status
git ls-remote https://github.com/<owner>/korea-talent-search-skills.git
```

### 로컬 개발 버전

```bash
npx skills add /Users/wbjung/korea-talent-search-skills --skill '*' --agent claude-code -y
```

## 3. 설치 확인

```bash
npx skills list --agent claude-code
```

아래 두 스킬이 보이면 정상이다.

```text
jobkorea-talent-search
saramin-talent-search
```

## 4. Claude Code 실행

```bash
claude
```

## 5. 잡코리아 검색 요청 예시

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

유료 열람, 마스킹 해제, 포지션 제안, 스크랩, 메모는 하지 마.
```

## 6. 사람인 검색 요청 예시

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

유료 열람, 연락처 확인, 포지션 제안, 스크랩, 메모는 하지 마.
```

## 7. 로그인/인증 원칙

- 로그인은 사용자가 열린 브라우저에서 직접 한다.
- 2차 인증이 나오면 사용자가 직접 완료한다.
- 비밀번호, OTP, SMS/메일 인증번호, 세션 쿠키는 Claude에게 알려주지 않는다.
- 인증 완료 후 Claude에게 “로그인했어” 또는 “인증 완료했어”라고 말한다.
- Claude는 같은 브라우저 세션에서 검색을 이어간다.

## 8. macOS 권한

Claude Code가 브라우저를 열거나 화면을 읽지 못하면, Claude Code를 실행한 앱에 권한을 준다.

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

권한 변경 후 터미널/Claude Code/브라우저를 재시작한다.


중요: 최종 Top 후보는 가능한 한 후보 상세/프로필 페이지까지 열어 현재 보이는 마스킹 상세 정보를 확인한 뒤 선정해야 한다. 상세 페이지를 보지 못하고 목록만 보고 추린 경우, 반드시 `목록 기반 1차 shortlist`라고 표시해야 한다. 상세 이력 확인 기반인지 목록 기반인지 반드시 구분한다.

## 9. 기대 결과

후보별 원문 URL은 필수다. URL을 못 찾으면 `URL: 추출 실패`와 이유를 적어야 한다.


각 스킬은 아래 형태로 결과를 반환해야 한다.

```text
인재 shortlist

검색 조건
- 포지션: ...
- 필수 조건: ...
- 우대 조건: ...
- 제외 조건: ...
- 경력/지역: ...
- 모드: 로그인 완료 브라우저 세션의 마스킹 후보 정보

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

## 10. 문제 해결

### 스킬이 설치되지 않는 경우

```bash
npx skills list --agent claude-code
```

없으면 다시 설치한다.

```bash
npx skills add <owner>/korea-talent-search-skills --skill '*' --agent claude-code -y
```

### Claude가 비로그인 정보만 보려는 경우

아래처럼 다시 지시한다.

```text
비로그인 정보로 진행하지 마.
브라우저를 열어서 기업회원 로그인 세션을 확인하고,
로그인된 마스킹 후보 정보를 읽을 수 없으면 결과를 확정하지 말고 원인을 알려줘.
```

### 2차 인증이 나오는 경우

정상일 수 있다. Claude가 연 브라우저가 평소 사용하는 Chrome과 다른 프로필이면 사이트가 새 기기로 인식할 수 있다.

```text
열린 브라우저에서 직원 본인이 직접 2차 인증을 완료한다.
```

## 11. 보안 및 개인정보 주의사항

반드시 지킬 것:

- 계정 비밀번호를 Claude에게 말하지 않는다.
- 2FA/OTP/SMS/메일 인증번호를 Claude에게 말하지 않는다.
- 세션 쿠키를 복사하거나 공유하지 않는다.
- 후보자 연락처/실명/개인정보를 외부 문서나 공개 채널에 저장하지 않는다.
- 유료 열람 여부는 인사 담당자가 직접 판단한다.
- 포지션 제안 발송은 반드시 담당자가 직접 확인 후 진행한다.
