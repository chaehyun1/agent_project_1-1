# Event 01 — Midweek Deadline Compression

- **id**: `event_01_deadline_compression`
- **version**: `0.1.0`
- **conflict_axes**: `time_compression`, `hard_vs_soft`, `context_switching`

## 요약

과제, 퀴즈, 팀플 회의가 수요일 저녁~목요일 오전에 몰린 상태에서 수면 부족이 누적된 에이전트가
**무엇을 먼저/나중에/포기할지**, 그리고 **누구에게 먼저 연락할지** 까지 long-horizon 으로
결정해야 하는 전형적인 복합 갈등 상황이다.

## 발동 조건

- 수요일 14:00 기준
- 선조건: 지난 이틀간 평균 수면 5시간 미만

## 상황 설정 (initial_state)

- **World**: 수요일 14:00, 캠퍼스 중앙도서관
- **Agent 상태**: energy 3/10, sleep_debt 6시간, stress 7/10
- **태스크**
    - `assignment_A` — 개인 과제, 마감 수요일 23:59, 진행률 0.5, **hard**
    - `quiz_prep` — 목요일 09:00 퀴즈 대비, 진행률 0.0, **hard**
    - `team_meeting` — 수요일 19:00 팀플 회의, **soft** (재협상 가능)
- **관계망**
    - teammate_A (trust 6, closeness 5) — 회의 주관자
    - professor (trust 7) — 과제 A 담당, office hour 참여 중
    - close_friend (trust 9, closeness 9) — 저녁 약속 요청 가능성

## 에이전트가 보는 정보 (visible_information)

- [Wed 14:05] LMS: "목요일 9시 퀴즈 공지 추가됨"
- [Wed 14:10] Team chat: "오늘 저녁 7시 회의 그대로 진행해요"
- [Wed 14:12] 개인 관찰: "과제 A 는 아직 절반 남았다"

## 숨은 상태 (hidden_state)

- `office_hour_reduces_workload` — 수요일 17:00 office hour 참석 시
  과제 A 난점이 해결되어 남은 작업량이 40% 축소됨 (노출 안 됨).
  → `discoverable_by`: 교수 일정을 확인하거나 과제 설명을 다시 읽는 action
- `sleep_debt_penalty_on_quiz` — 밤샘 시 목요일 퀴즈 집중력이 -40% 페널티.
  → `discoverable_by`: 자기 상태에 대한 자기 평가 action
- `teammate_will_accept_reschedule` — teammate_A 는 사전 제안 시 21:00 로 회의 조정 수용.
  → `discoverable_by`: 재협상 메시지 전송

## 진행 중 발생하는 이벤트 (events_queue)

- [Wed 16:30] teammate_A 가 "회의 자료 다 준비되셨나요?" 로 재확인
- [Wed 22:00] 에너지 1/10 도달 시 자동 피로 경고

## 평가 기준 (evaluation)

### Hard loss 조건
- assignment_A 미제출 또는 진행률 < 0.7 로 제출
- 목요일 퀴즈 응시 불가 (늦잠)
- teammate_A / professor 신뢰 -2 이상 하락

### Context switching 지표
- 과제 결정 시 "다음날 퀴즈/회의에 미치는 영향" 을 명시적으로 언급
- 메시지 송신 시 상대의 시간/기대까지 고려한 문구 사용

### Efficiency 지표
- 올나이트 대신 수면 블록 확보 + 아침 퀴즈 대비로 분배
- office hour 참석 → 과제 축소라는 제3의 경로 발견
- 팀 회의 재협상 시도 (전면 불참 대신)

### 좋은 대응 예시
"office hour 로 과제 A 난점을 해소하고, teammate_A 에게 21:00 로 회의 조정을 선제 요청한 뒤,
23:59 직전에 제출하고 6시간 수면을 확보해 아침에 퀴즈 대비."

### 나쁜 대응 예시
"세 가지 모두 혼자 밤샘으로 처리. 회의 무단 불참, 과제는 submit 했으나 퀴즈 점수 급락,
teammate_A 신뢰 하락."

## Duration

19시간 (수요일 14:00 → 목요일 09:00)
