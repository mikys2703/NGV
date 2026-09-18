# 함수 커버리지 · 콜 커버리지 100% 실측 방법 (오픈소스 도구)

`code-quality-gates` 스킬과 마찬가지로 "충분히 검증했다"는 눈대중 판단을 금지합니다. 두 지표 모두 실제 도구 실행 결과로 판정하세요.

## 사용 도구

| 목적 | 도구 | 비고 |
|---|---|---|
| 실행 라인 기록 | `coverage`(coverage.py) | `pip install coverage` — 이미 널리 쓰이는 표준 오픈소스 커버리지 도구 |
| 함수 목록·라인 범위 추출 | `lizard` | `code-quality-gates` 스킬이 이미 쓰는 도구를 재사용 — 새 도구를 추가하지 않아도 됨 |
| 정적 호출 그래프 추출 | `code2flow` | `pip install code2flow` — Python 소스에서 함수 간 정적 호출 간선을 뽑아냄 |
| 실행 중 실제 호출 간선 기록 | `sys.setprofile` 기반 트레이서(직접 작성, 표준 라이브러리만 사용) | 추가 설치 불필요 |

## 절차

### 1. 함수 목록과 라인 범위 확보

```bash
lizard <통합대상경로> --xml > functions.xml
```

`lizard`의 XML 출력에서 각 함수의 `name`, `start_line`, `end_line`을 추출해 전체 함수 목록을 만듭니다(도구가 설치돼 있지 않으면 설치 안내 후 실행하세요).

### 2. 통합시험 실행 중 라인 커버리지 수집

```bash
coverage run -m unittest discover -s <통합시험_디렉터리> -p "test_it_*.py"
coverage json -o coverage.json
```

`coverage.json`의 `files.<파일>.executed_lines`가 실행된 라인 번호 목록입니다.

### 3. 함수 커버리지 계산

각 함수의 `[start_line, end_line]` 구간에 `executed_lines` 중 하나라도 포함되면 그 함수는 "커버됨"으로 판정합니다.

```
함수 커버리지(%) = 커버된 함수 수 / 전체 함수 수 × 100
```

100%가 아니면 커버되지 않은 함수명·파일·라인을 그대로 보고하세요(뭉뚱그리지 마세요).

### 4. 정적 호출 그래프 추출

```bash
code2flow <통합대상경로> --output callgraph.json
```

`callgraph.json`의 edge 목록이 "이 함수가 저 함수를 호출할 수 있다"는 정적 호출 간선입니다.

### 5. 통합시험 실행 중 실제 호출 간선 기록

통합시험 실행 진입점(예: `python -m unittest` 실행 전)에 아래와 같은 트레이서를 걸어 실제로 일어난 (호출자→피호출자) 쌍을 기록합니다:

```python
import sys
import json

executedEdges = set()

def traceCalls(frame, event, arg):
    if event == "call":
        callerName = frame.f_back.f_code.co_name if frame.f_back else "<진입점>"
        calleeName = frame.f_code.co_name
        executedEdges.add((callerName, calleeName))
    return traceCalls

sys.setprofile(traceCalls)
# ... 통합시험 스위트 실행 ...
sys.setprofile(None)

with open("executed_edges.json", "w") as edgeFile:
    json.dump(sorted(executedEdges), edgeFile, ensure_ascii=False, indent=2)
```

### 6. 콜 커버리지 계산

`callgraph.json`의 정적 간선 집합을 `staticEdges`, `executed_edges.json`을 `executedEdges`라 하면:

```
콜 커버리지(%) = |staticEdges ∩ executedEdges| / |staticEdges| × 100
```

100%가 아니면 실행되지 않은 (호출자, 피호출자) 간선을 그대로 보고하세요.

## 100% 미달 시 처리 순서

1. 누락된 함수/호출 간선을 하나씩 지목합니다.
2. 그 함수/간선을 실제로 실행할 수 있는 통합시험 케이스가 `test-case-derivation-integration.md`의 근거(인터페이스 계약/통합 순서)로 도출 가능한지 확인하고, 가능하면 TPL-SWE5-002에 케이스를 추가합니다.
3. 근거를 도출할 수 없다면(예: 방어적 코드, 현재 통합 범위에서 도달 불가능한 분기) 그 이유를 SKILL.md 6단계의 산출물(템플릿 12장 "적용 한계" 또는 TPL-SWE5-003의 비고)에 명시적으로 문서화합니다. "일단 넘어간다"는 판단은 허용되지 않습니다.
4. 케이스를 추가했다면 2~6단계(측정)를 다시 실행해 100%에 도달했는지 재확인합니다.

## 보고 형식

```markdown
## 통합시험 구조적 커버리지 결과 — <대상 범위>

| 지표 | 결과 | 근거 |
|---|---|---|
| 함수 커버리지(목표 100%) | NN% | coverage.json + lizard 함수 목록 대조 |
| 콜 커버리지(목표 100%) | NN% | code2flow 정적 그래프 + executed_edges.json 대조 |

### 미달 항목과 처리
1. <함수/간선> — <추가한 시험 케이스 ID> 또는 <도달 불가능 근거>
```
