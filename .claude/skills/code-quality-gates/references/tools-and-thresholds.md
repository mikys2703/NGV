# 도구, 명령어, 임계값

## 설치

```bash
pip install lizard pylint radon
# doxygen은 Python 패키지가 아니라 별도 실행파일입니다.
# Windows: choco install doxygen.install (또는 공식 설치파일)
# Debian/Ubuntu: apt-get install doxygen
```

## 1. 함수 라인수 + 2. 순환복잡도 — `lizard`

```bash
lizard --length 50 --CCN 10 <대상 경로>
```

- `NLOC` 열이 함수의 순수 코드 라인(주석/공백 제외)입니다 — 50 초과 시 위반.
- `CCN` 열이 순환복잡도입니다 — 10 초과 시 위반(이 스킬이 채택한 기본 임계값, CLAUDE.md에 명시된 수치 아님).
- 임계값을 넘는 함수만 걸러서 보려면 `lizard --length 50 --CCN 10 -W <대상 경로>` (경고만 출력) 또는 종료 코드로 CI에서 실패 처리 가능(`--length`/`--CCN` 초과 시 lizard가 0이 아닌 종료 코드 반환).

## 3. 중복코드 — `pylint` (duplicate-code 체커)

```bash
pylint --disable=all --enable=duplicate-code --min-similarity-lines=8 <대상 경로>
```

- `min-similarity-lines=8`은 "8줄 이상 동일/유사하면 위반"을 의미합니다 — 즉 7줄까지는 허용된다는 CLAUDE.md 기준과 정확히 대응합니다.
- 이 체커는 여러 파일에 걸친 중복도 잡아냅니다 — 한 파일만 검사하면 놓칠 수 있으니, 관련된 모듈 전체를 대상 경로에 포함하세요.

## 4. 주석 비율 — `radon raw` + Doxygen 형식 — `doxygen`

비율 계산:

```bash
radon raw --json <대상 파일>
```

출력의 `loc`(전체 논리 줄 수), `comments`(단독 주석 줄), `multi`(독스트링 등 여러 줄 문자열)를 이용해 비율을 계산합니다:

```
주석비율 = (comments + multi) / loc
```

0.20 미만이면 위반입니다. 여러 파일을 한 번에 볼 때는 파일별로 계산 후 평균이 아니라 **파일마다 개별 판정**하세요 — 전체 평균이 20%를 넘어도 특정 파일이 0%일 수 있습니다.

Doxygen 형식 검증(주석이 실제로 `@brief`/`@param`/`@return`을 갖췄는지, 문서화 누락이 있는지)은 Doxygen 자체로 확인합니다:

```
# Doxyfile에 최소한 다음을 설정
EXTRACT_ALL = YES
WARN_IF_UNDOCUMENTED = YES
FILTER_PATTERNS = *.py=doxypypy   # Python 독스트링을 Doxygen이 이해하게 하는 필터 (pip install doxypypy)
```

```bash
doxygen Doxyfile 2> doxygen_warnings.log
```

`doxygen_warnings.log`에 나오는 "Member ... is not documented" 같은 경고가 있으면 그 함수/클래스에 Doxygen 형식 주석이 없거나 불완전한 것입니다 — `references/doxygen-comment-format.md`의 형식으로 채우세요.

## 5. 네이밍 — `pylint` (`.pylintrc`)

프로젝트 루트에 다음을 포함한 `.pylintrc`를 둡니다(없다면 이 스킬 실행 시 생성 제안):

```ini
[BASIC]
function-naming-style=camelCase
variable-naming-style=camelCase
argument-naming-style=camelCase
function-rgx=^[a-z][a-zA-Z0-9]{2,}$
variable-rgx=^[a-z][a-zA-Z0-9]{2,}$
argument-rgx=^[a-z][a-zA-Z0-9]{2,}$
```

`^[a-z][a-zA-Z0-9]{2,}$`는 "소문자로 시작 + 그 뒤로 최소 2글자 더(합쳐서 최소 3글자) + camelCase 허용 문자"를 의미합니다.

```bash
pylint --disable=all --enable=invalid-name <대상 경로>
```

`invalid-name` 경고가 뜬 식별자가 이 규칙 위반입니다. 클래스명(`class-naming-style`)은 이 스킬의 검사 대상이 아닙니다(CLAUDE.md는 함수명/변수명만 규정) — `.pylintrc`에서 `class-naming-style`은 건드리지 마세요.

## 한 번에 실행하기

여러 게이트를 각각 실행하는 대신 순서대로 스크립트처럼 실행해도 됩니다. 임계값 위반 시 0이 아닌 종료 코드를 반환하는 도구(lizard, pylint)는 그대로 CI 게이트로도 쓸 수 있습니다.
