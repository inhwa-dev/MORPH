# MORPH

## 파일별 역할

모프는 팀이 관리하는 모의 웹페이지에서 조회 업무를 실행하고, 화면 변경으로 기존 실행이 실패하면 대체 방법을 찾아 결과를 검증하는 프로젝트입니다. 아래 경로는 [프로젝트 폴더](capstone-macro-defender-prep-morph-kickoff/)를 기준으로 표시했습니다.

| 파일 | 담당 | 역할 |
| --- | --- | --- |
| [프로젝트 README.md](capstone-macro-defender-prep-morph-kickoff/README.md) | 공통 | 프로젝트 목표, 5인 역할 분담, 개발 시작 순서를 설명합니다. |
| [docs/kickoff.md](capstone-macro-defender-prep-morph-kickoff/docs/kickoff.md) | 공통 | 첫 작업 5개의 초안과 완료 기준, 회의에서 확정할 내용을 정리합니다. |
| [docs/handoff.md](capstone-macro-defender-prep-morph-kickoff/docs/handoff.md) | 공통 | 실행 요청, 조회 결과 등 모듈 간에 주고받는 데이터의 형식을 예시로 보여줍니다. |
| [backend/main.py](capstone-macro-defender-prep-morph-kickoff/backend/main.py) | A 실행 엔진 | 백엔드 진입점으로 사용할 예정입니다. 화면에서 오는 실행 요청을 받아 실행 엔진과 연결합니다. |
| [backend/runner/README.md](capstone-macro-defender-prep-morph-kickoff/backend/runner/README.md) | A 실행 엔진 | 모의 페이지 조작, 실패 기록, 대체 후보 실행을 맡는 실행 엔진의 범위를 설명합니다. |
| [frontend/mock-site/README.md](capstone-macro-defender-prep-morph-kickoff/frontend/mock-site/README.md) | B 모의 환경·테스트 | 정상 조회 화면과 버튼 이름·위치·구조가 바뀐 모의 페이지의 제작 범위를 설명합니다. |
| [tests/README.md](capstone-macro-defender-prep-morph-kickoff/tests/README.md) | B 모의 환경·테스트 | 정상 조회, 화면 변경 후 복구, 성공한 방법 재사용을 확인하는 통합 테스트 범위를 설명합니다. |
| [ai/recovery/README.md](capstone-macro-defender-prep-morph-kickoff/ai/recovery/README.md) | C 적응·복구 | 실패한 단계와 화면 정보를 받아 대체 후보를 고르고 A에 전달하는 역할을 설명합니다. |
| [ai/macro_training.ipynb](capstone-macro-defender-prep-morph-kickoff/ai/macro_training.ipynb) | C 적응·복구 | 후보 선택에 AI를 적용하게 되면 실험을 기록할 노트북 자리입니다. |
| [backend/experience/README.md](capstone-macro-defender-prep-morph-kickoff/backend/experience/README.md) | D 검증·저장 | 조회 결과의 일치 여부를 판정하고, 성공한 복구 방법을 저장·조회하는 범위를 설명합니다. |
| [backend/database.py](capstone-macro-defender-prep-morph-kickoff/backend/database.py) | D 검증·저장 | 데이터베이스를 사용하게 되면 복구 기록의 저장·조회 연결을 맡을 예정입니다. |
| [frontend/index.html](capstone-macro-defender-prep-morph-kickoff/frontend/index.html) | E 사용 화면·시연 | 입력창, 시작·중지 버튼, 실행 결과가 표시될 화면의 구조를 작성할 파일입니다. |
| [frontend/style.css](capstone-macro-defender-prep-morph-kickoff/frontend/style.css) | E 사용 화면·시연 | 화면 배치, 버튼 스타일, 실행 상태별 표시를 작성할 파일입니다. |
| [frontend/app.js](capstone-macro-defender-prep-morph-kickoff/frontend/app.js) | E 사용 화면·시연 | 버튼 동작, 백엔드 요청, 진행 상태 및 결과 표시를 구현할 파일입니다. |
| [requirements.txt](capstone-macro-defender-prep-morph-kickoff/requirements.txt) | 공통 | Python 패키지 목록 초안입니다. 실제 사용할 패키지는 기술 구성 확정 후 정리합니다. |

코드 파일의 설명은 개발을 시작할 때 맡을 역할을 기준으로 작성했습니다.
