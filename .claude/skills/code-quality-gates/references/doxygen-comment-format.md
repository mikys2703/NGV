# Doxygen 방식 주석 형식 (Python)

Python에는 Doxygen 전용 문법이 없으므로, 독스트링 안에 Doxygen 태그(`@brief`, `@param`, `@return`, `@throws` 등)를 쓰는 관례를 사용합니다. `doxypypy` 필터가 이를 Doxygen이 이해하는 형식으로 변환합니다.

## 함수/메서드

```python
def calculateLockState(speedKph, doorState, crashSignal):
    """!
    @brief 주행 속도, 도어 상태, 충돌 신호를 바탕으로 도어락 상태를 판정한다.

    @param speedKph 현재 차량 속도(km/h). 0 이상이어야 한다.
    @param doorState 각 도어의 개폐 상태 딕셔너리.
    @param crashSignal 충돌 감지 신호(True/False).

    @return 판정된 잠금 상태를 나타내는 LockState 열거값.

    @throws ValueError speedKph가 음수인 경우.
    """
    ...
```

- `"""!`로 시작해야 doxypypy가 이 독스트링을 문서화 대상으로 인식합니다.
- `@brief`는 한 줄 요약, 그 아래 필요하면 상세 설명을 추가할 수 있습니다.
- 파라미터가 있으면 **모든** 파라미터에 `@param`을 답니다 — 일부만 쓰면 Doxygen의 `WARN_IF_UNDOCUMENTED`가 나머지를 경고로 잡아냅니다.
- 반환값이 있으면 `@return`을, 명시적으로 발생시키는 예외가 있으면 `@throws`를 씁니다.

## 클래스

```python
class DoorLockController:
    """!
    @brief 후석 도어락의 판정과 액추에이터 제어를 담당하는 컨트롤러.
    """
```

## 모듈

파일 맨 위에 모듈 전체에 대한 `@brief`를 답니다:

```python
"""!
@file door_lock_controller.py
@brief 도어락 판정 로직과 액추에이터 인터페이스 구현.
"""
```

## 주석 비율 20%를 채우는 방법 — 양보다 정확성

주석 비율은 파일 전체 로직 줄 대비 계산되므로, 함수마다 위 형식을 빠짐없이 채우면 자연스럽게 20%에 가까워지는 경우가 많습니다. 그러나 비율을 억지로 채우려고 자명한 코드에 불필요한 한 줄 주석을 남발하지 마세요(예: `x = x + 1  # x에 1을 더한다`) — Doxygen 형식 주석(함수/클래스/모듈 단위)이 우선이고, 그래도 20%에 못 미치면 그때 비자명한 로직 옆에 "왜 이렇게 했는지"를 설명하는 인라인 주석을 추가하세요.

## 확인 방법

`tools-and-thresholds.md`의 `doxygen Doxyfile` 실행 결과에서 경고가 없고, `radon raw`로 계산한 비율이 0.20 이상이면 이 게이트를 통과한 것입니다.
