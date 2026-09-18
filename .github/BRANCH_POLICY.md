# 브랜치 정책 (AI 활용 vibe coding + GitHub 기준)

AI를 활용해 빠르게 반복 작업하는 1인/소규모 프로젝트에서 일반적으로 적용되는 GitHub 브랜치 정책입니다. 사람 리뷰어가 상시 존재하지 않는 대신, **`main`에 직접 반영되기 전에 반드시 PR을 거치고, `.github/workflows/ci.yml`의 자동 검사(CI)가 통과해야 한다**는 것이 핵심입니다.

## 브랜치 구조

- `main`: 보호 브랜치. 항상 배포/기준선 가능한 상태를 유지한다. 직접 `push` 금지, PR을 통해서만 반영한다.
- 작업 브랜치 명명 규칙(권장, GitHub이 강제하지는 않음):
  - `feature/<주제>`: 새 기능/스킬/서브에이전트 추가
  - `fix/<주제>`: 결함 수정
  - `chore/<주제>`: 문서, 설정, 템플릿 등 비기능 변경

## `main` 브랜치 보호 규칙

| 설정 | 값 | 이유 |
|---|---|---|
| PR을 통해서만 병합 허용(직접 push 금지) | 활성화 | 히스토리 추적성과 CI 통과를 강제하기 위함 |
| 필수 상태 검사(Required status checks) | `build-and-test`(이 저장소의 CI 워크플로우 job) | CI가 실패하면 병합 버튼이 비활성화됨 |
| 병합 전 브랜치를 base와 최신 상태로 유지(Require branches to be up to date) | 활성화 | 오래된 브랜치가 최신 `main`과 충돌 없이 검증됐다는 착각을 방지 |
| 관리자에게도 규칙 적용(Enforce for administrators) | 활성화 | "관리자니까 예외"로 규칙을 우회하지 않도록 함 — vibe coding에서 실수로 규칙을 끄고 강제 반영하는 사고를 막는 핵심 장치 |
| 필수 리뷰어 승인 수 | 0 (선택적 권장 0~1) | 1인/AI 페어 프로그래밍 환경에서는 사람 승인 대신 CI 통과가 실질적 게이트 역할을 한다. 팀원이 합류하면 1 이상으로 올릴 것 |
| Force push 허용 | 금지 | `main` 히스토리를 임의로 덮어쓰지 못하게 함 |
| 브랜치 삭제 허용 | 금지 | `main` 자체를 실수로 삭제하지 못하게 함 |
| 병합 후 head 브랜치 자동 삭제 | 활성화(저장소 설정) | 작업 브랜치가 쌓이지 않게 정리 |

## 적용 방법

### 방법 A — GitHub CLI로 직접 적용 (권장)

로컬에 `gh` CLI가 설치·인증되어 있다면(`gh auth login`), 아래 명령으로 위 표의 규칙을 그대로 적용합니다. 이 세션의 샌드박스에는 `gh`가 없어 직접 실행할 수 없으니, 사용자 터미널에서 실행하세요:

```bash
gh api --method PUT repos/mikys2703/NGV/branches/main/protection \
  --input - <<'EOF'
{
  "required_status_checks": {
    "strict": true,
    "checks": [{ "context": "build-and-test" }]
  },
  "enforce_admins": true,
  "required_pull_request_reviews": null,
  "restrictions": null,
  "allow_force_pushes": false,
  "allow_deletions": false,
  "required_linear_history": false
}
EOF

# 병합 후 작업 브랜치 자동 삭제(저장소 설정, 브랜치 보호와 별개)
gh api --method PATCH repos/mikys2703/NGV -f delete_branch_on_merge=true
```

`required_status_checks.checks`의 `context` 값(`build-and-test`)은 `.github/workflows/ci.yml`의 job id와 정확히 일치해야 합니다 — job id를 바꾸면 이 값도 함께 바꾸세요.

### 방법 B — GitHub 웹 UI에서 수동 적용

1. 저장소 → **Settings → Branches → Add branch ruleset**(또는 구버전 UI의 **Branch protection rules → Add rule**)
2. Branch name pattern: `main`
3. "Require a pull request before merging" 체크 (Required approvals는 0으로 두거나 팀 상황에 맞게)
4. "Require status checks to pass before merging" 체크 → `build-and-test` 선택, "Require branches to be up to date before merging" 체크
5. "Do not allow bypassing the above settings"(관리자 예외 없음) 체크
6. "Allow force pushes" 비활성화, "Allow deletions" 비활성화
7. 저장 후, 저장소 **Settings → General → Pull Requests**에서 "Automatically delete head branches" 체크

## CI 워크플로우와의 관계

`.github/workflows/ci.yml`은 PR이 `main`을 대상으로 열리거나 갱신될 때(`pull_request`) 자동 실행되며, 단위 테스트(`unittest`)와 `code-quality-gates` 스킬의 5가지 게이트(함수 라인수·순환복잡도·중복코드·주석비율·네이밍)를 동일한 명령으로 재실행합니다. 위 브랜치 보호 규칙에서 이 워크플로우를 필수 상태 검사로 지정해야 실제로 "게이트"로 작동합니다 — 워크플로우만 추가하고 브랜치 보호를 적용하지 않으면 실패해도 병합이 막히지 않습니다.
