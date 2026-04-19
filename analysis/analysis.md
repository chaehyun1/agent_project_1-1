# Analysis — Conflict Axes, Evaluation Metrics, and Coverage Matrix

이 문서는 이벤트 × 페르소나 라이브러리가 **무엇을** 측정하려 하는지, 그리고 각 이벤트가
평가 축들을 어떻게 커버하는지 정리한다. 개별 이벤트의 상세 평가 지표는 각 `events/*.md` 의
`evaluation` 섹션에서 중복 없이 참조한다.

## 1. 세 가지 평가 축

### 1.1 Hard vs Soft
에이전트가 **되돌릴 수 없는 손실**과 **조정 가능한 비용**을 구분하는지 평가한다.

- **Hard 손실 예시**: 마감 미준수로 인한 성적 페널티, 건강 악화(수면 부족 누적 → 퀴즈 실패),
  신뢰 관계의 파손, 복구 불가능한 결정
- **Soft 비용 예시**: 일정 재조정으로 인한 약간의 불편, 장소 이동, 선호 활동 연기
- **실패 패턴**: Hard 를 Soft 로 착각 (예: "조금만 밤새우면 돼" → 다음날 퀴즈 실패)
- **성공 패턴**: Hard 를 방어하기 위해 Soft 를 의도적으로 희생

### 1.2 Context Switching
한 도메인(학업)에서 내린 결정이 다른 도메인(관계, 건강)에 미치는 영향을 **명시적으로** 고려하는지 평가한다.

- **측정 방법**: 에이전트의 추론 trace / 메시지 내용에서 교차 도메인 언급 빈도
- **실패 패턴**: 단일 도메인 최적화 (예: 과제만 생각하고 팀원에게 연락 없음 → 신뢰 하락)
- **성공 패턴**: 결정 시 다른 도메인에 주는 2차 효과를 미리 언급하고 완화책 포함

### 1.3 Efficiency
극단적인 양자택일(올나이트 / 전면 포기) 대신 **제3의 대안**을 탐색하는지 평가한다.

- **측정 방법**: 선택된 action sequence 가 극단 선택인지, 하이브리드/부분 대안인지
- **실패 패턴**: "다 하거나 다 포기하거나" 에 갇힘. 숨은 상태(hidden_state)를 탐색하지 않음
- **성공 패턴**: office hour 참석, 부분 참여, 장소 스위칭 같은 제3의 경로 발견

## 2. Conflict Axes Taxonomy

이벤트를 분류하는 태그 체계. 한 이벤트는 여러 태그를 가질 수 있다.

| 태그 | 정의 |
|------|------|
| `time_compression` | 여러 hard 마감이 짧은 구간에 몰림 |
| `social_vs_self` | 관계 유지와 자기 계획 실행이 충돌 |
| `hard_vs_soft` | Hard/Soft 구분 능력이 직접 평가되는 구조 |
| `information_asymmetry` | 결정적 정보가 hidden_state 에 있음 |
| `context_switching` | 둘 이상의 도메인이 동시에 움직임 |
| `long_vs_short_term` | 즉각 이득과 장기 비용이 대립 |
| `value_conflict` | 페르소나의 상위 가치 두 개가 충돌 |
| `recovery_after_mistake` | 잘못된 선택 이후 회복 경로 탐색 |
| `routine_vs_novelty` | 고정 루틴과 돌발 기회가 충돌 |

## 3. 커버리지 매트릭스 (현재)

| 이벤트 | time_compression | social_vs_self | hard_vs_soft | info_asym | context_switch | long_short | value_conf | recovery | routine_nov |
|--------|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| [event_01_deadline_compression](../events/event_01_deadline_compression.md) | ● | | ● | ○ | ● | ○ | | | |
| [event_02_campus_routine](../events/event_02_campus_routine.md) | | ● | | ○ | ● | | | | ● |

- `●` 1차 축 (primary)
- `○` 2차 축 (incidental)

### 빠진 커버리지 (후속 이벤트 제안)

- `information_asymmetry` 를 primary 로 테스트하는 이벤트 (결정적 정보가 hidden 에 있음)
- `value_conflict` 이벤트 (학업 vs 건강 중 하나를 선택해야 하는 구조)
- `recovery_after_mistake` 이벤트 (이미 잘못된 결정을 내린 후의 대응)
- `long_vs_short_term` 이벤트 (즉각적 유혹과 장기 이득의 대립)

## 4. 이벤트 × 페르소나 조합이 드러내는 것

같은 이벤트라도 페르소나에 따라 예상되는 약점과 평가 결과가 달라진다. 아래는 기대되는 패턴이다.

| 페르소나 \ 이벤트 | event_01 (deadline) | event_02 (routine) |
|-------------------|---------------------|--------------------|
| [persona_01_balanced_sophomore](../personas/persona_01_balanced_sophomore.md) | 기준선. 재협상·office hour 활용 가능성 높음 | 30분 부분 합의 경로 선택 기대 |
| [persona_02_tired_achiever](../personas/persona_02_tired_achiever.md) | **위험군.** 올나이트 + 무연락으로 hard loss 유발 가능성 큼 | 사회 합류 자체가 낮아 덜 도전적 |
| [persona_03_social_explorer](../personas/persona_03_social_explorer.md) | 팀 회의 재협상은 잘하나 수면 관리 부실 위험 | **위험군.** 전면 합류로 3시간 공강 낭비 가능성 |

이 매트릭스는 각 페르소나의 **예측되는 실패 모드**를 드러내며, 에이전트(LLM)가 페르소나의 약점을
인지하고 교정하는지가 core evaluation 이다.

## 5. Sandbox 구현 시 권장 사항 (범위 밖이지만 기록)

- `hidden_state.discoverable_by` 필드의 action 이름은 sandbox action space 와 1:1 매칭되어야 한다.
- NPC(teammate, professor, friend_group) 는 초기에는 스크립트 규칙으로 시작하고,
  `relationship.trust/closeness` 를 인자로 받는 간단한 확률 모델을 권장한다.
- Trajectory 로깅은 `(timestamp, state_snapshot, action, observation)` 튜플 시퀀스로 저장하면
  `evaluation` 필드의 지표를 후처리로 채점하기 쉽다.
