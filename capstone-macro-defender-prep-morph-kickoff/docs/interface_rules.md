# MORPH 공통 함수·데이터 규칙 (회의용 초안 v0.1)

> **목적:** A~E가 각자 개발해도 나중에 쉽게 연결하도록 **함수 이름, 입출력, 실행 상태**를 미리 맞춘다. 이 문서는 개발 규칙의 **제안안**이며, 첫 회의에서 확정한다.
>
> **1차 범위:** 팀이 관리하는 모의 웹페이지에서 `CS101` 강좌를 조회한다. 사이트의 버튼 이름·위치·구조를 변경해 복구와 재사용을 시험한다. 기존 [모듈 연동 예시](handoff.md)와 같은 데이터 필드를 사용한다.

## 1. 이름과 파일 규칙

- **Python 함수/파일:** `snake_case` (예: `propose_recovery`, `run_engine.py`)
- **JavaScript 함수:** `camelCase` (예: `startRun`)
- **JSON 키와 상태값:** 영어 소문자 `snake_case` (예: `run_id`, `failed_step`)
- 같은 의미는 **이름을 하나만 사용**한다. 예: 실행 식별자는 모두 `run_id`이며 `job_id`, `session_id`로 제각각 부르지 않는다.
- 함수는 결과를 반환하고, 문제가 생기면 정해진 오류/상태를 반환한다. 다른 담당자의 파일을 직접 수정하지 말고 연결 규칙을 먼저 맞춘다.
- 공통 규칙을 바꿀 때는 GitHub **PR에서 A~E 영향 범위를 확인**한 후 변경한다.

## 2. 담당자별 공통 함수 (첫 주에 이름부터 합의)

| 담당 | 함수 이름 제안 | 입력 → 출력 | 주의 |
| --- | --- | --- | --- |
| **A 실행 엔진** | `start_run(request)` | 실행 요청 → `run_id`, 상태 | 전체 흐름 연결 담당 |
| A | `stop_run(run_id)` | 실행 ID → 중지 결과 | 중지 요청 후 추가 행동 금지 |
| A | `execute_step(step, context)` | 수행할 클릭/조회 단계 → 실행 결과 | C가 후보를 제시해도 **실제 실행은 A** |
| **B 모의 환경·테스트** | `classify_failure(failure)` | 실패 정보 → 실패 원인 코드 | 정상 페이지·변경 페이지 관리 |
| B | `get_expected_result(course_code)` | 강좌 코드 → 기대 조회 결과 | D 검증에 활용 |
| **C 적응·복구** | `propose_recovery(failure, saved_method=None)` | 실패 정보·기존 방법 → 대체 후보 목록 | C는 후보를 찾고 **직접 클릭하지 않음** |
| **D 검증·경험 저장** | `find_saved_method(failure)` | 실패 상황 → 저장된 방법 또는 `None` | C가 새로운 후보를 만들기 전 조회 |
| D | `verify_result(expected, actual)` | 기대 결과·실제 결과 → 성공/실패 판정 | 클릭 성공만으로 조회 성공 처리하지 않음 |
| D | `save_method(method, verification)` | 복구 방법·검증 결과 → 저장 결과 | **검증 성공한 방법만 저장** |
| **E 사용 화면** | `startRun()`, `stopRun()`, `renderStatus()` | 사용자 입력·실행 상태 → 화면 표시 | A와 JSON/API로 연결 |

> Python 함수의 `async` 여부, 실제 모듈 위치와 HTTP API 사용 여부는 팀이 기술 구성을 확정한 뒤 결정한다. **지금은 이름·입력·출력의 의미를 먼저 통일**한다.

## 3. 모두가 쓰는 JSON 형식

### 3-1. E → A : 시작 요청

```json
{
  "task_id": "course_lookup",
  "course_code": "CS101"
}
```

### 3-2. A → E : 실행 시작과 완료

```json
{
  "run_id": "demo-001",
  "status": "running",
  "recovery": "none"
}
```

```json
{
  "run_id": "demo-001",
  "status": "completed",
  "course": {
    "code": "CS101",
    "name": "컴퓨터 개론"
  },
  "recovery": "new"
}
```

- **`status`:** `running`(진행 중) / `completed`(검증 후 성공) / `failed`(실패) / `stopped`(사용자 중지) / `needs_user`(사용자 인증·확인 필요)
- **`recovery`:** `none`(기본 방식) / `new`(새 복구 방법) / `reused`(저장 방법 재사용)
- 모든 실행에 **동일한 `run_id`**를 붙여 A~E의 로그와 화면을 연결한다.
- 시간 기록이 필요하면 ISO 8601 형식(시간대 포함)을 사용한다. 비밀번호·인증 토큰은 로그에 기록하지 않는다.

### 3-3. A → B → C : 실행 실패 정보

```json
{
  "run_id": "demo-001",
  "task_id": "course_lookup",
  "failed_step": "click_search",
  "error_code": "ELEMENT_NOT_FOUND",
  "observation": {
    "visible_button_names": ["강좌 검색"]
  }
}
```

- B는 원인을 `LAYOUT_CHANGED` / `AUTH_REQUIRED` / `OTHER` 중 하나로 분류해 C/A에 전달한다.
- `AUTH_REQUIRED`이면 **복구 클릭을 진행하지 않고** `needs_user` 상태로 전환한다.

### 3-4. C → A : 대체 동작 후보

```json
{
  "candidates": [
    {
      "action": "click",
      "locator": {
        "strategy": "role",
        "role": "button",
        "name": "강좌 검색"
      }
    }
  ]
}
```

- 후보가 없다면 `{"candidates": []}`를 반환한다.
- 후보는 **제안일 뿐 성공이 아님**. A가 실행하고, D가 실제 조회 결과를 검증한 뒤에만 성공 처리한다.
- 선택자(Locator)는 실행 가능한 데이터로 표현하고, 다른 모듈에서 임의의 코드 문자열을 실행하지 않는다.

### 3-5. D : 검증과 저장

```json
{
  "verified": true,
  "reason": "RESULT_MATCH"
}
```

- `course.code`와 해당 코드의 기대 정보가 일치할 때에만 `verified: true`.
- D가 검증한 새 방법만 저장한다. 저장한 방법도 다음 실행 때 다시 검증한다.
- 검증 실패 시 방법을 저장하지 않고 A가 `failed`로 종료하거나 C의 다른 후보를 시도한다.

## 4. 처음 연결할 순서

1. **B**가 정상/변경 모의 페이지와 기대 결과를 먼저 공유한다.
2. **A + E**가 `start_run` → 기본 조회 → `completed`까지 한 번 연결한다.
3. **A + B + C**가 버튼 변경 시 실패 정보 → 대체 후보 → 재실행을 연결한다.
4. **A + D**가 결과 검증 후 새 복구 방법 저장을 연결한다.
5. **C + D**가 저장된 방법을 다시 불러와 적용하고, **E**가 `new` / `reused`를 표시한다.

**통합 책임:** A는 모듈 연결·PR 병합을 관리하고, B는 통합 테스트를 주관한다. 각 담당자는 자기 코드와 테스트 결과를 책임진다.

## 5. PR 올리기 전에 확인할 것

- [ ] 위의 함수 이름과 JSON 키가 일치하는가?
- [ ] 성공·실패·사용자 확인 필요 상태가 구분되는가?
- [ ] 실제 실행이 없어도 예시 입력(Mock)으로 테스트할 수 있는가?
- [ ] 다른 담당자에게 전달할 입력/출력 예시가 있는가?
- [ ] `main`에서 기존 정상 조회 기능이 깨지지 않는가?

**회의에서 확정할 항목:** (1) 역할별 담당자, (2) 함수 이름·JSON 필드, (3) 첫 모의 조회 화면과 기대 결과, (4) 첫 통합 날짜.
