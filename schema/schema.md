# Schema Definition

이 문서는 [event_schema.json](event_schema.json) 과 [persona_schema.json](persona_schema.json) 의
설계 의도를 사람이 읽을 수 있도록 풀어 쓴 것이다. 실제 validation 은 `.json` 스키마를 기준으로 한다.

## 설계 원칙

1. **`.md` ↔ `.json` 1:1 페어링** — 모든 이벤트/페르소나는 동일한 `id` 를 공유하는
   두 파일(서술형 `.md` / 구조형 `.json`) 을 갖는다. `.md` 는 리뷰 용, `.json` 은 sandbox consumer 용.
2. **Visible vs Hidden 의 명시적 분리** — 에이전트가 보는 정보(`visible_information`) 와
   결과에 영향을 주지만 보이지 않는 변수(`hidden_state`) 를 스키마 차원에서 분리한다.
   숨은 상태를 탐색하는 능력 자체가 평가 대상이기 때문이다.
3. **평가 hook 을 이벤트 정의 안에 내장** — `evaluation` 필드에 hard_loss / context_switching /
   efficiency 세 축의 관찰 지표를 함께 기술한다. 이벤트와 채점 기준이 분리되면 관리 비용이 폭증한다.
4. **State 레이어 분리** — 페르소나의 `baseline_state` 는 "에이전트가 누구인가" 를 정의하고,
   이벤트의 `initial_state.agent` 는 "그 이벤트 시작 순간 상태" 를 정의한다. 동일 페르소나라도
   이벤트마다 다른 시작 상태를 가질 수 있다 (예: 이틀 연속 수면 부족 후 진입).

## Event 스키마 핵심 필드

| 필드 | 역할 |
|------|------|
| `id` | stable snake_case identifier. 파일명, 로그, cross-reference 의 키 |
| `conflict_axes` | 분류 태그. analysis 문서의 커버리지 매트릭스와 매칭 |
| `trigger` | 이벤트 발동 조건 (시각, 선조건) |
| `initial_state` | 월드/에이전트/태스크/관계 시작 상태. 각 태스크는 `hardness: hard|soft` 를 갖는다 |
| `visible_information` | t=0 에 에이전트가 보는 inbox/관찰 이벤트 |
| `hidden_state` | 결과에 영향을 주지만 노출되지 않는 변수. `discoverable_by` 로 발견 action 명시 |
| `events_queue` | 시뮬레이션 도중 fire 되는 스케줄드 이벤트 |
| `available_actions` | 이 이벤트에서 의미 있는 action 프리미티브 힌트 (sandbox 참고용) |
| `evaluation` | 3축 평가 지표 (hard_loss_conditions / context_switching_indicators / efficiency_indicators) |

## Persona 스키마 핵심 필드

| 필드 | 역할 |
|------|------|
| `baseline_state` | 기본 에너지·수면·스트레스·체력·집중력 |
| `value_priorities` | 학업·관계·건강·성장·여가 가중치 (≈ 합 1) |
| `communication_style` | assertiveness / directness / conflict_style |
| `relationship_network` | 역할별 신뢰·친밀도. 이벤트의 NPC 해상 시 사용 |
| `decision_tendencies` | planning_horizon / risk_tolerance / procrastination_bias / sleep_discipline |

## 페어링 규칙 요약

- `events/<id>.md` ↔ `events/<id>.json`
- `personas/<id>.md` ↔ `personas/<id>.json`
- `id` 는 경로/파일명/JSON 내부 `id` 세 곳에서 모두 동일해야 한다.
- `.json` 의 key 와 `.md` 의 섹션 제목은 의미적으로 대응되도록 작성한다
  (`initial_state.tasks` ↔ `## 상황 설정 > 태스크`, etc.).
