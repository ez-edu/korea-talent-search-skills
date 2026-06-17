# jobkorea-talent-search

잡코리아 기업회원 로그인 세션에서 현재 보이는 마스킹 이력서/목록을 비교해 유료 열람 전 shortlist를 만드는 스킬이다.

번들 설치 권장:

```bash
npx --yes skills add ez-edu/korea-talent-search-skills --skill "*" --agent codex -g -y
```

핵심 흐름:

1. 잡코리아 기업 인재검색 페이지를 연다.
2. 사용자가 열린 브라우저에서 직접 로그인한다.
3. 검색 조건을 입력하고 마스킹 목록/이력서를 읽는다.
4. 유료 열람 추천 후보를 점수, 근거, 리스크, URL과 함께 정리한다.
5. 유료 열람/연락처 확인/제안/스크랩/메모는 자동 실행하지 않는다.

비로그인 fallback helper:

```bash
python3 scripts/jobkorea_talent_search.py --keyword "퍼포먼스 마케터 GA4" --work-area "서울" --limit 20
```

직원용 빠른 가이드는 저장소 루트의 `docs/employee-claude-guide.md`, Windows 설치/문제해결은 `docs/windows-codex-setup.md`를 참고한다.
