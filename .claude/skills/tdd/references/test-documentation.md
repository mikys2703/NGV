# 테스트 함수 문서화 (기법 / 긍정·부정 케이스 / Doxygen 목적 설명)

이 스킬로 작성하는 **모든 테스트 함수**는 아래 세 가지를 예외 없이 문서화해야 합니다.

1. 사용한 테스트 기법(technique)
2. 긍정(Positive) 케이스인지 부정(Negative) 케이스인지
3. Doxygen 형식의 테스트 목적 설명(`@brief`)

프로덕션 코드의 Doxygen 형식은 `code-quality-gates` 스킬의 `references/doxygen-comment-format.md`를 따르며, 테스트 함수도 동일한 `"""! ... """` 형식을 사용합니다 — 다른 문법을 만들지 마세요.

## 형식

```python
def test<행동을 설명하는 이름>(self):
    """!
    @brief <이 테스트가 무엇을 검증하는지 한 줄 요약>
    @details 기법: <기법명> | 케이스: <긍정|부정>
    """
    ...
```

- `@brief`는 "무엇을 검증하는가"를 한 문장으로 씁니다. `references/writing-good-tests.md`의 원칙 1(잡아내는 고장을 이름으로 설명하라)에 따라, 이 테스트가 실패한다면 어떤 프로덕션 코드 변경 때문인지가 드러나야 합니다.
- `@details`의 `기법`은 아래 표의 기법명 중 하나를 그대로 씁니다. 여러 기법이 겹치면 `,`로 나열합니다(예: `경계값분석, 오류추정법`).
- `케이스`는 반드시 `긍정` 또는 `부정` 둘 중 하나입니다.

## 기법 분류 (`test-case-derivation.md`와 대응)

| 기법명 | 정의 | 출처(상세설계) | 케이스 유형 |
|---|---|---|---|
| 동등분할법 | 사전조건을 만족하는 대표 입력 클래스 하나를 검증 | 함수 계약의 사전조건(정상 입력) | 긍정 |
| 오류추정법 | 사전조건을 위반하는 입력이 명시된 예외/오류를 내는지 검증 | 함수 계약의 사전조건(위반 입력), 예외/오류 필드 | 부정 |
| 경계값분석 | 최소/최대/0/음수/빈 입력 등 경계 근처 값을 검증 | 핵심 알고리즘의 경계값 | 경계값이 유효 범위 안이면 긍정, 범위를 벗어나면 부정 |
| 의사결정표기반테스트 | 정책 의사결정표의 한 행이 나타내는 조건 조합과 기대 결과를 검증 | 정책 의사결정표 | 그 행의 기대 결과가 정상 동작이면 긍정, 거부/예외/차단이면 부정 |
| 상태전이기반테스트 | 상태 머신의 한 전이(현재 상태 + 이벤트 → 다음 상태)를 검증 | 상세설계의 상태 전이 정의(있는 경우) | 정의된 전이면 긍정, 금지된 전이(전이 거부)면 부정 |

이 표에 없는 상황이 나오면 임의로 새 기법명을 만들지 말고, 가장 가까운 기법으로 분류하거나 사용자에게 확인하세요.

## 예시

```python
class TestDoorLockDecision(unittest.TestCase):

    def testLocksWhenSpeedAboveThresholdAndAllDoorsClosed(self):
        """!
        @brief 속도가 임계값을 넘고 모든 도어가 닫혀 있으면 잠금 상태로 판정되는지 검증한다.
        @details 기법: 의사결정표기반테스트(정책 의사결정표 2번째 행) | 케이스: 긍정
        """
        ...

    def testKeepsLockedWhenReleaseRequestedWhileDrivingFast(self):
        """!
        @brief 주행 중 해제 요청이 들어와도 안전 규칙에 따라 잠금이 유지되는지 검증한다.
        @details 기법: 의사결정표기반테스트(정책 의사결정표 3번째 행) | 케이스: 부정
        """
        ...

    def testRaisesValueErrorOnNegativeSpeed(self):
        """!
        @brief 속도가 음수로 주어지면 ValueError가 발생하는지 검증한다.
        @details 기법: 오류추정법(사전조건 위반) | 케이스: 부정
        """
        with self.assertRaises(ValueError):
            calculateLockState(-1, doorState, crashSignal=False)

    def testLocksExactlyAtSpeedThresholdBoundary(self):
        """!
        @brief 속도가 임계값과 정확히 같을 때(경계값)의 판정을 검증한다.
        @details 기법: 경계값분석 | 케이스: 긍정
        """
        ...
```

## 하지 말아야 할 것

- `@brief` 없이 `@details`만 쓰지 마세요 — Doxygen 경고(`WARN_IF_UNDOCUMENTED`)가 발생합니다.
- 기법명을 생략하거나 "기타"로 뭉뚱그리지 마세요 — 표의 기법명 중 하나를 명시해야 어떤 근거(상세설계의 어느 부분)에서 나온 테스트인지 추적됩니다.
- 긍정/부정을 테스트 이름만으로 판단하게 두지 말고 `@details`에 명시하세요 — 검토자가 이름을 잘못 해석할 수 있습니다.
- 이 문서화를 나중에 "한꺼번에" 채우지 마세요 — Red 단계에서 테스트를 작성하는 시점에 함께 씁니다.
