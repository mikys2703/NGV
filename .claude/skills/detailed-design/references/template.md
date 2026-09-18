# 상세설계서 양식 (TPL-SWE3-001)

조직 실제 템플릿: `WP_Templates/Engineering/SoftwareDetailedDesignAndUnitConstruction/TPL-SWE3-001_SW 상세설계서 템플릿.docx`. 다른 양식을 임의로 만들지 말고 이 구조를 그대로 따르세요.

## 문서 통제 정보

- 템플릿 ID: `TPL-SWE3-001` / 적용 프로세스: `SWE.3`
- 실제 산출물 명명 규칙: `ENG-SWE3-001_SW 상세설계서` (UML/호출관계 원본은 `ENG-SWE3-002_상세설계 UML 및 호출관계 원본`), 완성본은 `02_Tier/02_WorkProduct`에 둡니다(경로가 없으면 임의로 만들지 말고 사용자에게 확인).
- 표지·변경이력·작성/검토/승인 상태 관례는 `architecture-design` 스킬의 `references/template.md`와 동일합니다 — "실제 승인 여부"와 "시나리오 상태"를 구분해서 정직하게 표시하세요.

## 본문 15개 절과 각 절의 작성 기준

| 절 | 작성 안내(원문) | 이 스킬에서 채우는 방법 |
|---|---|---|
| 1. 목적 및 적용범위 (1.1/1.2/1.3) | 문서 필요 이유, 적용 대상, 포함/제외 범위와 경계 | 대상 아키텍처 요소와 그 범위를 그대로 인용 |
| 2. 모듈 분해 | 아키텍처 요소를 구현 단위로 분해, ID·책임·소스 위치 | SKILL.md 2단계 |
| 3. 상세 호출관계 | 함수/클래스 호출 순서, 의존 방향, 데이터 흐름 | SKILL.md 3단계, `uml-call-diagram.md` |
| 4. 공통 자료형 | 공통 데이터 구조, 열거형, 단위, 범위, 불변조건, 직렬화 규칙 | SKILL.md 4단계 |
| 5. 핵심 함수 계약 | 입력/출력/사전조건/사후조건/부작용/예외/시간제약 | SKILL.md 5단계, `contracts-and-decision-tables.md` |
| 6. 핵심 알고리즘 | 처리순서/판단조건/경계값/계산근거 (의사코드/흐름도) | SKILL.md 5단계, `contracts-and-decision-tables.md` |
| 7. 정책 의사결정표 | 입력조건 조합/우선순위/기대동작/충돌해결 규칙 | SKILL.md 5단계, `contracts-and-decision-tables.md` |
| 8. 상태전이 상세 | 상태 저장 위치, 전이함수, 이벤트, 가드, 타이머, 초기화 | SKILL.md 6단계 |
| 9. Web 및 API 상세 | 엔드포인트, 요청/응답, 검증, 오류코드, 세션, 보안경계 | SKILL.md 7단계, `contracts-and-decision-tables.md`의 API 계약 |
| 10. 오류와 방어 동작 | 유효하지 않은 입력/예외/자원실패의 검출·처리·기록·복구 | SKILL.md 8단계, `iso26262-part6-unit-design.md` |
| 11. 코딩 및 검증 규칙 | 코딩표준, 정적분석, 단위검증, 커버리지, 리뷰기준 | SKILL.md 9단계, `iso26262-part6-unit-design.md` |
| 12. 단위와 요구사항 할당 | 구현단위 ↔ 아키텍처 요소, SW요구사항, 단위시험 연결 | SKILL.md 10단계 |
| 13. 구현 경계 | 생성코드, 외부라이브러리, 플랫폼종속부, 구현 제외 범위 | SKILL.md 10단계, 외부 라이브러리는 ENG-SBOM-001에 등록 안내 |
| 14. 추적성 | 아키텍처-상세설계-소스파일-함수-단위시험 양방향 연결 | SKILL.md 11단계, `traceability-unit.md` |
| 15. 참고자료 | 아키텍처 설계서, 코딩규칙, API 문서, 외부 라이브러리 자료 | 참조한 ENG-SWE2-001 등 문서 ID를 명시 |

## 짝을 이루는 UML 원본 파일

`TPL-SWE3-002_상세설계 UML 및 호출관계 템플릿.drawio`가 이 문서의 다이어그램 원본 양식입니다. 표현 방식은 `references/uml-call-diagram.md`를 따르세요.

## 관련 산출물 — SBOM/FOSS

같은 프로세스 그룹(`SoftwareDetailedDesignAndUnitConstruction`)에 `TPL-SBOM-001_Python 의존성 SBOM FOSS 라이선스 목록 템플릿.xlsx`(적용 프로세스 SWE.3/SUP.8)이 있습니다. 이 스킬이 직접 채우는 문서는 아니지만, 13장(구현 경계)에서 외부 라이브러리를 도입했다면 이 문서(ENG-SBOM-001)에 등록해야 한다는 것을 사용자에게 알려주세요.

## 이 프로젝트의 특수 맥락

`TPL-SBOM-001`이 "Python 의존성"을 다루는 것에서 알 수 있듯, 이 교육용 프로젝트(VJ-ECL-2026)의 구현 언어는 Python입니다. `references/iso26262-part6-unit-design.md`의 원칙을 적용할 때 이 사실을 반영하세요(예: 포인터 제한 같은 C 계열 전용 원칙을 기계적으로 옮기지 않기). 다른 언어로 범위가 바뀌었다면 사용자에게 확인하세요. 다른 산출물과 마찬가지로 실제 인증·승인·ASIL 달성을 주장하지 않는 고지 관례도 유지하세요.
