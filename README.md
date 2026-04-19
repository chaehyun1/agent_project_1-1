# Agent Project — Event / Persona Design & Analysis

이 저장소는 LLM 기반 에이전트의 **long-horizon self-management** 능력을 평가하기 위한
**이벤트(상황) × 페르소나(에이전트 프로필)** 라이브러리를 설계하는 것을 목표로 한다.

실제 sandbox(실행 환경) 구현은 후속 단계이며, 본 저장소의 범위는
**(1) 공유 가능한 설계 스펙** 과 **(2) 평가 축에 대한 분석 문서** 를 납품하는 것까지다.

## 산출물 개요

| 디렉토리 | 내용 | 형식 |
|----------|------|------|
| [schema/](schema/) | Event / Persona 스키마 정의. sandbox 팀이 그대로 파싱 가능 | `.md` + `.json` |
| [events/](events/) | 개별 이벤트 명세. 상황·숨은 상태·평가 기준 포함 | `.md` + `.json` |
| [personas/](personas/) | 에이전트 baseline 프로필 (state / values / relationships) | `.md` + `.json` |
| [analysis/](analysis/) | conflict 축, 평가 지표, 이벤트×페르소나 매트릭스 분석 | `.md` |
| [situation.md](situation.md) | 최초 아이디에이션 문서 (원형 이벤트 2개) | `.md` |

## 형식 규칙

- `.md` 는 **사람이 읽고 리뷰**하기 위한 서술형 문서다.
- `.json` 은 **sandbox / 평가 파이프라인이 그대로 import** 하기 위한 구조화 버전이며,
  항상 동일한 `id` 를 공유하는 `.md` 와 **1:1 페어**로 유지한다.
- 모든 `.json` 은 [schema/event_schema.json](schema/event_schema.json) 또는
  [schema/persona_schema.json](schema/persona_schema.json) 을 준수한다.

## 평가 축 (analysis에서 자세히)

에이전트의 대응을 세 축에서 관찰한다.

1. **Hard vs Soft** — 되돌릴 수 없는 손실(마감, 건강, 신뢰 파손)과 조정 가능한 비용을 구분하는가
2. **Context Switching** — 한 도메인의 결정이 다른 도메인에 미치는 영향을 명시적으로 고려하는가
3. **Efficiency** — 극단 선택(올나이트, 전면 포기) 대신 제3의 대안을 탐색하는가


