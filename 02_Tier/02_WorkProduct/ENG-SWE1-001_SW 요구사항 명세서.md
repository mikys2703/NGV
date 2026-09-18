# ENG-SWE1-001_SW 요구사항 명세서

| 필드 | 값 |
|---|---|
| 템플릿 ID | TPL-SWE1-001 |
| 적용 프로세스 | SWE.1 |
| 프로젝트 | VJ-ECL-2026 (전자식 차일드락 제어 SW) |
| 작성 문서 ID와 명칭 | ENG-SWE1-001_SW 요구사항 명세서 |
| 버전 및 베이스라인 | v0.1 (draft) — 목표 베이스라인 BL-SWR-1.0 (G1) |
| 상위 입력 | `OEM-SWR-001_OEM SW 요구사항 사양서` Rev 1.0 (BL-OEM-1.0) |
| 상태 | 초안 — Phase 1 리뷰 대기 |

> 교육용 가상 프로젝트(VJ-ECL-2026) 산출물이다. 실제 OEM 승인, A-SPICE 능력수준, ISO 26262 준수·인증·ASIL 달성을 주장하지 않는다. 형식은 초안 단계(Markdown, git 리뷰용)이며, 승인 시 `docx` 스킬로 `TPL-SWE1-001` 실제 양식에 전사한다(`references/template.md`의 산출물 형식 기본 결정).

---

## 1. 목적 및 적용범위

### 1.1 목적
`OEM-SWR-001`이 공급자(본 프로젝트)에게 할당한 전자식 차일드락 제어 SW 요구를 소프트웨어 요구사항(SWR) 수준으로 구체화하고, 검증 가능한 수용기준과 검증방안을 부여해 이후 아키텍처(SWE.2)·상세설계(SWE.3)·구현/시험(SWE.4~6)이 그대로 이어받아 쓸 수 있는 계약을 만든다.

### 1.2 적용범위
대한민국 판매용 2026년식 가상 OEM-A 승용차 후석 좌/우 전자식 차일드락 제어 SW. ASIL B 안전요구(4종)와 QM 기능/비기능 요구(9종)를 모두 포함한다. 실행 환경은 Python(§7 참고) PC/SIL 참조 구현과 Web 시뮬레이터.

### 1.3 적용 경계
SW-only PC/SIL 및 Web 검증만 다룬다. HIL, 실차, 타깃 ECU, HW 개발, HARA/ASIL 재산정, 공식 인증·심사는 범위 밖이다(OEM-SWR-001 1.3절과 동일 경계를 그대로 상속).

---

## 2. 요구사항 작성 및 판정 규칙

### 2.1 식별 및 상태 규칙
- ID 체계는 **OEM-SWR-001의 9절(추적성)이 이미 지정한 `SWR-###` 체계를 그대로 사용**한다(`requirements-analysis` 스킬 기본 접두사 `SWE-REQ-####` 대신 — 상위 입력이 지정한 체계 우선 원칙).
- ID는 재사용하지 않는다. 폐기되는 항목은 결번으로 남긴다.
- 상태값: 초안 → 리뷰중 → 합의 → 기준선(baseline).

### 2.2 품질 판정 기준
모든 SWR은 EARS 문형(단일 SHALL), 정량적 수용기준, 실행 가능한 검증방안, 양방향 추적(상위 OEM ID / 하위 검증 산출물)을 갖춰야 완결로 본다. 기준 미달 항목은 "초안" 상태를 유지한다.

---

## 3. 상태와 우선순위
- **상태**: 초안(작성 완료, 미검토) / 리뷰중(PR 오픈) / 합의(리뷰 승인) / 기준선(main merge, BL-SWR-1.0 태깅).
- **우선순위**: 필수(이 문서의 모든 SWR은 OEM 할당 요구의 직접 파생이므로 전부 필수) / 권고(현재 없음).
- 전이 조건: 리뷰중→합의는 사용자 승인, 합의→기준선은 main merge(브랜치 정책·CI 통과 전제).

---

## 4. 기능 및 안전 관련 SW 요구사항

### 4.1 기능 요구사항 (QM)

| SWR ID | 출처(OEM) | 요구사항 (EARS) | 수용기준 | 검증방안 |
|---|---|---|---|---|
| SWR-001 | OEM-FR-001 | WHEN 유효한 운전자 명령(side, action)이 수신되면, THE 차일드락 제어 SW SHALL 지정된 도어(들)에 해당 action(LOCK/RELEASE)을 적용한다. | 정지·정상입력에서 4개 source × LOCK/RELEASE × LEFT/RIGHT/ALL 조합이 선택 출력에만 적용됨(9절 검증수준: PC/SIL/Web) | 요구기반시험(sw-system-test) |
| SWR-002 | OEM-FR-001 | THE 차일드락 제어 SW SHALL side=all 명령을 좌·우 도어 모두에, side=left\|right 명령을 해당 도어에만 적용하고 반대쪽 출력을 변경하지 않는다. | 좌/우 단독 명령 시 반대쪽 출력 불변 확인 | 요구기반시험 |
| SWR-003 | OEM-FR-002 | WHEN 유효한 vehicle_speed_kph가 3km/h 이상으로 평가되면, THE 차일드락 제어 SW SHALL 그 평가주기부터 좌·우 도어를 LOCK한다. | 3km/h 이상 첫 평가주기에 두 출력 LOCK | 요구기반시험 + 경계값(2.9/3.0/3.1km/h) |
| SWR-004 | OEM-FR-001 | THE 차일드락 제어 SW SHALL RELEASE 명령(SWR-001)을 §4.2의 억제/강제 우선순위 조건(SWR-005, 007, 017, 018, 021)에 해당하지 않는 경우에만 적용한다(운전자 해제 명령은 최하위 우선순위). | §4.2 우선순위표의 각 조건 하에서 RELEASE 명령이 거부/지연됨을 확인 | 요구기반시험 + 의사결정표 전 행 |
| SWR-006 | OEM-SR-002, OEM-FR-003 | WHEN 도어 D에 대한 접근위험 억제(SWR-005) 발생 후 10초 이내에 같은 도어 D에 대한 동일 RELEASE 명령이 재입력되면, THE 차일드락 제어 SW SHALL 이를 명시적 override로 처리해 RELEASE를 허용하고 override 상태·이유코드를 생성한다. | 억제 후 경과시간 ≤10초 재입력에서 override + 이유코드 생성, >10초는 억제 유지 | 요구기반시험 + 경계값(9.9/10.0/10.1s) |
| SWR-010 | OEM-NFR-002 | THE 차일드락 제어 SW SHALL 각 제어결정을 이벤트ID·타임스탬프·좌/우 출력·판정 이유코드로 구성된 고정 스키마로 기록하고, 최근 100건만 순환 보존한다(101번째 입력 시 가장 오래된 1건 제거). | 101건 입력 후 최신 100건만 순서대로 조회, 허용 필드만 존재 | 단위시험 + 통합시험 |
| SWR-011 | OEM-NFR-002 | THE 차일드락 제어 SW SHALL 결정 이력을 프로세스 메모리 내에서만 유지하고, 프로세스 재기동 시 이전 이력을 복원하지 않는다. | 재기동 후 이력 조회 결과 0건 | 단위시험 |
| SWR-012 | OEM-NFR-002 | THE 차일드락 제어 SW SHALL 결정 이력에 영상·음성·개인식별정보를 포함하지 않고, adult_present는 불리언 값으로만 기록한다. | 이력 레코드 필드 검사 시 허용 필드(이벤트ID/시각/출력/이유코드/불리언 입력값)만 존재 | 검사(코드 리뷰) + 단위시험 |
| SWR-014 | OEM-FR-004 | WHEN 상태조회 요청이 수신되면, THE 차일드락 제어 SW SHALL 좌/우 출력, state, 입력 유효성, 최근 결정 이유코드를 포함한 응답을 제공한다. | 상태조회 응답에 4개 필드 모두 포함 | 통합시험 + Web 시연 |
| SWR-015 | OEM-FR-004, OEM-IF-006 | THE 차일드락 제어 SW SHALL 상태조회 응답을 OEM-IF-006 계약(state/priority_reason/reason_code/input_validity)으로 직렬화하고, 직렬화 실패 시 HTTP 500을 반환한다. | 정상 직렬화 및 강제 실패 주입 시 HTTP 500 확인 | 통합시험 |
| SWR-016 | OEM-NFR-001 | THE 차일드락 제어 SW SHALL 동일한 입력 순서와 초기 상태에 대해 항상 동일한 제어결과를 생성한다. | 고정 시계로 같은 입력벡터 1,000회 재생 시 결과 해시 전부 동일 | 시스템(자격)시험 — 재현성 전용 케이스 |
| SWR-017 | OEM-FR-005 | WHEN fire_detected, overtemperature_detected, adult_present 중 하나 이상이 유효한 TRUE로 확인되면, THE 차일드락 제어 SW SHALL 다음 평가주기에 좌·우 도어를 RELEASE하고 입력별 이유코드를 기록한다. | 각 입력 단독 주입 시 다음 평가주기에 두 출력 RELEASE + 입력별 이유코드 | 요구기반시험(3개 입력 각각) |
| SWR-018 | OEM-FR-006 | WHEN isofix_left 또는 isofix_right가 TRUE로 확인되면, THE 차일드락 제어 SW SHALL 다음 평가주기에 해당 도어만 LOCK하고 반대쪽 출력을 변경하지 않는다. | 한쪽 ISOFIX만 TRUE 시 해당 출력만 LOCK, 반대쪽 유지 | 요구기반시험 |
| SWR-020 | OEM-FR-007 | WHEN ignition_on이 FALSE로 평가되면, THE 차일드락 제어 SW SHALL 다음 평가주기에 좌·우 출력을 RELEASE로, state를 OFF로 전환하고 이유코드를 ignition_off로 설정한다. | ignition_on=FALSE 첫 평가주기에 두 출력 RELEASE + state=OFF + 이유코드 확인 | 요구기반시험 |

> **SWR-005, SWR-009**는 안전 관련(ASIL B)이므로 §4.2에 배치. **SWR-019**는 OEM-SWR-001 9절 추적성표에 결번으로 존재 — §8 가정/미결정 참고.

### 4.2 안전 관련 SW 요구사항 (ASIL B)

| SWR ID | 출처(OEM) | ASIL | 요구사항 (EARS) | 수용기준 | 검증방안 |
|---|---|---|---|---|---|
| SWR-005 | OEM-SR-002 | B | WHILE 도어 D의 rear_left_approach_risk 또는 rear_right_approach_risk가 TRUE인 동안, IF 도어 D에 대한 RELEASE 명령이 수신되면, THEN THE 차일드락 제어 SW SHALL 그 명령을 거부하고 도어 D를 LOCK 상태로 유지하며 억제 사유를 기록한다. | 접근위험 TRUE 시 해당 출력 LOCK, RELEASE 요청에도 LOCK 유지, 사유 기록 | 요구기반시험 + 오류주입시험 |
| SWR-007 | OEM-SR-001 | B | WHEN crash_status가 CONFIRMED로 평가되면, THE 차일드락 제어 SW SHALL §4.2 우선순위표(아래)의 다른 모든 조건보다 우선하여 좌·우 도어에 RELEASE를 300ms 이내에 적용하고, 이후 입력 주기에도 유지한다. | 유효 충돌 입력 시 두 출력 300ms 이내 RELEASE, 이후 주기 유지 | 요구기반시험 + 타이밍 분석 |
| SWR-008 | OEM-SR-001 | B | WHILE crash_status가 PENDING인 동안, THE 차일드락 제어 SW SHALL SWR-007을 발동하지 않고 §4.2 우선순위표의 나머지 조건을 그대로 적용한다. | PENDING 상태에서 SWR-007 미발동, 다른 조건 정상 적용 확인 | 요구기반시험 (§8 가정 A-2 참고) |
| SWR-009 | OEM-SR-002 | B | THE 차일드락 제어 SW SHALL rear_left_approach_risk와 rear_right_approach_risk를 도어별로 독립 평가하여, 한쪽만 TRUE일 때 반대쪽 도어 출력에 영향을 주지 않는다. | 한쪽 접근위험만 TRUE 시 반대쪽 출력 불변 | 요구기반시험 |
| SWR-013 | OEM-SR-003 | B | IF 안전 관련 필수 입력이 마지막 갱신 이후 200ms를 초과해 갱신되지 않으면, THEN THE 차일드락 제어 SW SHALL 100ms 이내에 DEGRADED 상태로 전이한다. IF 입력이 정의된 형식·범위를 벗어나면, THEN THE 차일드락 제어 SW SHALL 그 입력을 평가에 사용하기 전에 거부한다. | 200ms 초과 미갱신 시 100ms 이내 DEGRADED, 형식/범위 오류는 평가 전 거절 | 단위시험 + 요구기반시험 + 경계값 |
| SWR-021 | OEM-SR-004 | B | WHEN 정규화된 입력의 sensor_fault가 TRUE로 확인되면, THE 차일드락 제어 SW SHALL 그 평가주기부터 새 명령을 적용하지 않고 직전 확정 출력을 유지하며 state를 FAULT로, 경고코드를 발생시킨다. 단, SWR-007(충돌 긴급해제)은 이 유지 상태보다 우선 적용된다(§8 가정 A-1). | sensor_fault=TRUE 첫 평가주기에 좌·우 출력 유지 + FAULT 상태 + 경고코드 | 요구기반시험 + 오류주입시험 |

#### 정책 우선순위표 (모든 §4.1/4.2 조건의 통합 의사결정)

이 표는 개별 SWR에 흩어진 우선순위 서술을 하나의 결정 순서로 통합한 것이다(도어별로 독립 평가되는 SWR-009/018은 도어 단위로 이 표를 각각 적용). 아키텍처(SWE.2)·상세설계(SWE.3)의 정책 의사결정표 입력으로 그대로 재사용한다.

| 순위 | 조건 | 결과 | 근거 SWR | 비고 |
|---|---|---|---|---|
| 0 | 입력 형식/범위 오류, 또는 stale(>200ms) | 해당 신호 미신뢰 → DEGRADED(100ms 내) | SWR-013 | 판단 이전 전처리 게이트 |
| 1 | crash_status = CONFIRMED | 양쪽 RELEASE (300ms 내) | SWR-007 | **최상위 — §8 가정 A-1: sensor_fault보다도 위** |
| 2 | sensor_fault = TRUE (그리고 순위1 아님) | 직전 확정 출력 유지, state=FAULT | SWR-021 | |
| 3 | 해당 도어 approach_risk = TRUE (그리고 순위1,2 아님) | 그 도어 LOCK, RELEASE 억제(단 SWR-006 오버라이드 시 예외) | SWR-005, SWR-006, SWR-009 | 도어별 독립 |
| 4 | fire_detected/overtemperature_detected/adult_present 중 하나 TRUE (그리고 상위 순위 아님) | 양쪽 RELEASE | SWR-017 | **§8 가정 A-3: ISOFIX보다 위(인명 우선)** |
| 5 | 해당 도어 isofix = TRUE (그리고 상위 순위 아님) | 그 도어 LOCK | SWR-018 | 도어별 독립 |
| 6 | ignition_on = FALSE (그리고 상위 순위 아님) | 양쪽 RELEASE, state=OFF | SWR-020 | |
| 7 | vehicle_speed_kph ≥ 3km/h (그리고 상위 순위 아님) | 양쪽 LOCK | SWR-003 | |
| 8 | 그 외 — 운전자 명령 적용 | 명령대로 LOCK/RELEASE | SWR-001, SWR-002, SWR-004 | 최하위 |

---

## 5. 입력 데이터 사전

| 데이터 | 출처 인터페이스 | 자료형/범위 | 유효성 조건 | 오류 처리 |
|---|---|---|---|---|
| vehicle_speed_kph | OEM-IF-001 | float, 0.0~300.0 km/h | 범위 내, freshness ≤200ms | 범위 밖/미갱신 → SWR-013 |
| gear | OEM-IF-001 | enum {P,N,D,R} | 정의된 값만 | 미정의 값 → INVALID(SWR-013) |
| source_timestamp_s | OEM-IF-001 | float, s | 단조 증가 | 역행/누락 → INVALID |
| crash_status | OEM-IF-002 | enum {NONE,PENDING,CONFIRMED} | 정의된 값만 | 미정 값 → INVALID |
| rear_left/right_approach_risk | OEM-IF-003 | boolean | — | 누락/형식오류 → INVALID |
| side, action, source | OEM-IF-004 | enum(각 §OEM-IF-004) | 정의된 enum만 | 누락/형식/미등록 enum → 거절 |
| lock_left/right (출력) | OEM-IF-005 | enum {LOCK,RELEASE} | — | — (PC/SIL 논리 출력까지만 검증) |
| state, priority_reason, reason_code, input_validity (출력) | OEM-IF-006 | enum/string | — | 직렬화 실패 → HTTP 500 |
| fire_detected, overtemperature_detected, adult_present | OEM-IF-007 | boolean | — | 누락/형식오류 → INVALID |
| isofix_left/right | OEM-IF-008 | boolean | — | 누락/형식오류 → INVALID |
| ignition_on, sensor_fault | OEM-IF-009 | boolean | — | 누락/형식오류 → INVALID |

---

## 6. 외부 인터페이스 요구

각 OEM-IF-###는 SW 수준에서 §5의 유효성 조건과 SWR-013(형식/범위/신선도 게이트)을 통과한 뒤에만 §4의 판정 로직에 입력된다. OEM-IF-005(액추에이터 출력)와 OEM-IF-006(표시 출력)은 SWR-015(직렬화 계약)로 구체화된다. OEM-IF-004(운전자 명령)는 SWR-001/002로 구체화된다.

---

## 7. 비기능 및 환경 제약

- **실행환경**: CLAUDE.md 표준에 따라 **Python 3.14** PC/SIL 참조 구현(§8 가정 A-4 — OEM 문서는 "Python 3.12"로 서술하나 프로젝트 표준을 우선). Web 시뮬레이터로 시험 입력/결과 가시화.
- **평가주기**: OEM 요구사항이 "평가주기", "다음 평가주기"를 반복 언급하나 주기 길이를 명시하지 않음 — §8 가정 A-5로 처리(SWR-007의 300ms, SWR-013의 100/200ms 요건을 만족하도록 아키텍처 단계에서 평가주기를 확정해야 함, 권장 상한 50ms 이하로 아키텍처에 제안).
- **결정론(NFR-001)**, **이력 휘발성/용량(NFR-002)**: §4.1 SWR-010/011/012/016 참고.

---

## 8. 분석 결과와 가정 (Assumptions & Open Items)

이 절의 항목은 OEM-SWR-001에 명시되지 않아 이번 SWE.1 분석에서 판단을 내린 것이다. **G1(BL-SWR-1.0) 확정 전 OEM 확인을 권장한다.**

| ID | 가정/미결정 | 근거 | 영향 |
|---|---|---|---|
| A-1 | 충돌 긴급해제(SWR-007)가 센서고장 출력유지(SWR-021)보다 우선한다 | 생명안전(탈출/구조)이 데이터 무결성보다 우선한다는 일반 안전원칙. OEM 원문은 두 조건의 상대 순위를 명시하지 않음 | §4.2 우선순위표 순위1 vs 2. 틀리면 정책 의사결정표 전체 재작업 필요 — **가장 위험도 높은 가정** |
| A-2 | crash_status=PENDING 동안은 일반 우선순위(표의 순위2~8)를 그대로 적용하고 SWR-007을 발동하지 않는다 | OEM 원문이 PENDING의 동작을 정의하지 않음, "CONFIRMED가 되면"이라는 조건문으로부터 PENDING≠CONFIRMED를 역해석 | SWR-008 |
| A-3 | 강제해제(화재/과온/성인탑승, SWR-017)가 ISOFIX 강제잠금(SWR-018)보다 우선한다 | 탈출/인명 관련 강제해제가 오조작 방지 목적의 ISOFIX 강제잠금보다 상위라는 판단. 두 조건이 같은 도어에서 동시 성립하는 사례는 원문에 없음 | §4.2 우선순위표 순위4 vs 5 |
| A-4 | 구현/CI 표준 Python 버전은 3.14(CLAUDE.md)로 하고, OEM 문서의 "3.12" 서술은 예시적 환경 기술로 간주한다 | CLAUDE.md 및 이미 구성된 CI(`ci.yml`)가 3.14 기준 | §7. 실제 납품 환경이 3.12로 고정이면 CI·스킬 전체 재조정 필요 |
| A-5 | 평가(제어) 주기 길이가 원문에 없어, 아키텍처 단계에서 확정하도록 남긴다(권장 상한 50ms) | SWR-007(300ms), SWR-013(100/200ms) 요건을 만족해야 하므로 주기가 이보다 충분히 짧아야 함 | Phase 1 아키텍처(SWE.2) 설계 입력 |
| A-6 | SWR-019는 OEM-SWR-001 9절 추적성표에 결번으로 존재하며, 이 문서에서 임의로 채우지 않고 결번으로 유지한다 | OEM 원문 확인 결과 SWR-001~021 중 019만 누락 | 추적 매트릭스에도 결번으로 표시 |
| A-7 | SWR-006(재입력 오버라이드)은 SR-002(ASIL B)의 예외 처리이지만 FR-003이 QM으로 지정했으므로 QM 등급을 유지한다 | OEM 원문이 FR-003을 QM으로 명시 | §4.1에 배치, §4.2 우선순위표에서만 안전 로직과 교차 참조 |

---

## 9. 하향 할당 및 검증 계획

| SWR 범위 | 설계 할당(예정) | 검증 수준 | 검증방법(스킬) | 상태 |
|---|---|---|---|---|
| SWR-013, IF 계약 전체 | Phase 1 아키텍처 — 입력검증 컴포넌트 | 단위 | `tdd`+`code-quality-gates` | 예정 |
| SWR-005,006,007,008,009,021 | Phase 2 — 안전 우선순위 결정 엔진 | 단위/통합/시스템 | `tdd`, `integration-testing`, `sw-system-test` | 예정 |
| SWR-017,018,020 | Phase 3 — 강제상태 로직 | 단위/통합/시스템 | 동일 | 예정 |
| SWR-001,002,003,004,006 | Phase 4 — 운전자 명령/자동잠금 | 단위/통합/시스템 | 동일 | 예정 |
| SWR-010,011,012,014,015,016 | Phase 5 — 표시/이력/결정론 | 단위/통합/시스템 | 동일 | 예정 |
| 전체 | Phase 6 — Web 통합 및 종합 시스템시험 | 시스템 | `sw-system-test` (전체 회귀) | 예정 |

아키텍처 요소 ID는 Phase 1의 `architecture-design` 산출물 확정 후 이 표와 §11 추적성 매트릭스에 채운다.

---

## 10. 범위 밖 주장

이 문서와 향후 산출물은 SW-only PC/SIL·Web 검증 결과만 근거로 한다. HIL, 실차, 도어 래치/구동기 물리 안전, 형식승인/인증, 대한민국 법규(자동차규칙 별표 14/14의2) 적합성 판정은 이 문서의 범위 밖이며, OEM-SWR-001 7절이 지정한 "후속 확인" 책임(OEM-A 담당)에 속한다.

---

## 11. 추적성

| Upper Req (OEM) | SW Req | 설계(예정) | 검증(예정) | 상태 |
|---|---|---|---|---|
| OEM-FR-001 | SWR-001, SWR-002, SWR-004 | TBD (Phase 4) | TBD | 계획 |
| OEM-FR-002 | SWR-003 | TBD (Phase 4) | TBD | 계획 |
| OEM-FR-003 | SWR-006 | TBD (Phase 2/4) | TBD | 계획 |
| OEM-FR-004 | SWR-014, SWR-015 | TBD (Phase 5) | TBD | 계획 |
| OEM-FR-005 | SWR-017 | TBD (Phase 3) | TBD | 계획 |
| OEM-FR-006 | SWR-018 | TBD (Phase 3) | TBD | 계획 |
| OEM-FR-007 | SWR-020 | TBD (Phase 3) | TBD | 계획 |
| OEM-SR-001 | SWR-007, SWR-008 | TBD (Phase 2) | TBD | 계획 |
| OEM-SR-002 | SWR-005, SWR-006, SWR-009 | TBD (Phase 2) | TBD | 계획 |
| OEM-SR-003 | SWR-013 | TBD (Phase 1) | TBD | 계획 |
| OEM-SR-004 | SWR-021 | TBD (Phase 2) | TBD | 계획 |
| OEM-NFR-001 | SWR-016 | TBD (Phase 5) | TBD | 계획 |
| OEM-NFR-002 | SWR-010, SWR-011, SWR-012 | TBD (Phase 5) | TBD | 계획 |
| — | SWR-019 (결번) | — | — | §8 A-6 |

이 표는 `ENG-TRC-001`(`TPL-TRC-001`) 매트릭스의 **Upper Req / SW Req** 열 초안이다. 실제 xlsx 매트릭스 동기화는 Phase 1 PR 리뷰 승인 후 별도로 반영한다(§ 이 문서 상단의 산출물 형식 기본 결정 참고).

---

## 12. 참고자료

- `OEM_Sample/OEM-SWR-001_OEM SW 요구사항 사양서.docx` (Rev 1.0, BL-OEM-1.0) — 유일한 상위 입력
- `CLAUDE.md` — 프로젝트 개발 표준(Python 3.14, 테스트/커버리지 지침)
- `WP_Templates/PRC-TPL-001_표준 산출물 양식 등록부.xlsx` — 산출물 명명/경로 규칙
- ISO 26262-6:2018 (선정 SW 활동 경계, 교육용 부분 적용)
