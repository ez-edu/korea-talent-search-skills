# 채용 인재검색 스킬 직원용 빠른 가이드

`korea-talent-search-skills`는 잡코리아/사람인 기업회원 화면에서 현재 보이는 마스킹 후보 정보를 읽고, 유료 열람 전에 shortlist를 만드는 Codex용 스킬 묶음이다.

포함 스킬:

```text
jobkorea-talent-search
saramin-talent-search
```

Windows 설치/문제해결은 별도 문서를 참고한다.

```text
docs/windows-codex-setup.md
```

## 설치

```powershell
npx --yes skills add ez-edu/korea-talent-search-skills --skill "*" --agent codex -g -y
```

확인:

```powershell
npx --yes skills list --agent codex -g
```

## 사용 원칙

- 직원 본인이 열린 브라우저에서 직접 로그인/2차 인증한다.
- Codex에게 비밀번호, OTP, 인증번호, 쿠키를 알려주지 않는다.
- Codex는 마스킹/현재 표시 정보만 읽는다.
- 유료 열람, 연락처 확인, 제안 발송, 스크랩, 메모, 상태 변경은 직원이 직접 판단한다.
- 최종 추천에는 후보 URL과 검토 수준이 있어야 한다.

## 잡코리아 요청 예시

```text
jobkorea-talent-search 스킬을 사용해서 잡코리아에서 퍼포먼스 마케터 후보를 찾아줘.

조건:
- 경력: 3~7년
- 지역: 서울
- 필수: Meta Ads, Google Ads, GA4, 퍼포먼스 마케팅
- 우대: 앱 마케팅, CRM, SQL, 데이터 기반 의사결정
- 제외: 콘텐츠 제작만 담당한 후보
- ROAS, CAC, LTV, 전환율 개선 경험을 높게 평가
- 유료 열람 추천 Top 5

목록만 보고 Top 5를 확정하지 말고 가능한 후보 상세/프로필 페이지를 열어 현재 보이는 마스킹 상세 정보까지 확인해줘.
각 후보마다 URL과 검토 수준을 표시해줘.
유료 열람, 연락처 확인, 포지션 제안, 스크랩, 메모는 하지 마.
```

## 사람인 요청 예시

```text
saramin-talent-search 스킬을 사용해서 사람인 인재풀에서 B2B 세일즈 후보를 찾아줘.

조건:
- 경력: 4~10년
- 지역: 서울/경기
- 필수: B2B 영업, 신규 고객 발굴, 계약 협상, CRM 사용 경험
- 우대: SaaS, 교육/HR 업종, ARR 또는 매출 목표 달성 경험
- 제외: 단순 매장 판매 또는 텔레마케팅 중심
- 매출 성과, 파이프라인 관리, 고객군 적합도를 높게 평가
- 유료 열람 추천 Top 5

검색/필터/후보 상세 읽기는 진행해도 되지만 유료 열람, 연락처 확인, 제안 발송, 스크랩, 메모는 하지 마.
후보별 URL과 검토 수준을 포함해줘.
```

## 결과에서 확인할 것

```text
- 검색 조건이 맞는지
- Top N 후보마다 URL이 있는지
- 상세 이력 확인 기반인지, 목록 기반 1차 shortlist인지
- 점수와 근거가 직무에 맞는지
- 유료 열람/연락처 확인/제안 발송을 Codex가 실행하지 않았는지
```

## 막히면

- 로그인/2차 인증 화면: 직원이 직접 완료하고 “로그인했어”라고 입력.
- 브라우저 권한 문제: Codex가 브라우저 제어 권한을 요청하면 직원이 직접 허용.
- 회사 보안 정책으로 설치 실패: 우회하지 말고 IT 요청 문구를 Codex에게 만들게 한다.
- Windows 설치 문제: `docs/windows-codex-setup.md` 참고.
