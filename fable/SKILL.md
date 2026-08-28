---
name: fable
description: Fable 5 심층추론 라우팅 토글. 사용자가 "fable on" / "fable off" / "fable status" / "/fable"라고 할 때 사용. on이면 무거운 추론·아키텍처 설계·근본원인 분석을 Fable 5 서브에이전트로 위임하고, off면 Opus로 되돌린다. 비싼 Fable을 최고난도 판단에만 쓰고 나머지(일반 구현 Opus, 잡무 Haiku)는 그대로 두어 비용을 아끼기 위한 스위치.
---

# Fable 심층추론 토글

"심층추론 티어"만 Fable 5 ↔ Opus로 바꾸는 스위치다. 나머지 오케스트레이션(일반 구현 → Opus, 단순 잡무 → Haiku)은 건드리지 않는다.

상태 저장 위치: `D:\클로드\workspace\CLAUDE.md`의 "모델 오케스트레이션" 섹션 안
`**Fable 심층추론 티어: ON/OFF**` 줄. (이 줄이 세션 시작 때 로드되어 기본값이 된다.)

## 인자에 따른 동작

### `fable on`
1. `D:\클로드\workspace\CLAUDE.md`에서 `Fable 심층추론 티어:` 줄 값을 `ON`으로 Edit.
2. 이후 라우팅: **무거운 추론·아키텍처 설계·근본원인 분석 → `Agent(model="fable", effort="high"~"max")`**.
   일반 구현·수정·테스트·리뷰 → `Agent(model="opus")`, 단순 명령·조회·빌드·로그 → `Agent(model="haiku", effort="low")` (변동 없음).
3. Park에게 확인: "Fable 심층추론 ON — 최고난도 추론/설계만 Fable 5로, 나머지는 Opus/Haiku 유지."

### `fable off`
1. 같은 줄을 `OFF`로 Edit.
2. 무거운 추론·설계 → `Agent(model="opus", effort="high"~"max")`로 되돌린다. (Fable 토큰 소비 0)
3. Park에게 확인: "Fable OFF — 심층추론도 Opus로 복귀."

### `fable status` (또는 인자 없음)
`Fable 심층추론 티어:` 현재 값과 그 의미를 한 줄로 보고한다.

## 반드시 지킬 사실 (검증됨 2026-07-10)

- **세션 리드 모델과 무관하게 작동한다.** 리드가 Opus 4.8(높음)이어도 `Agent(model="fable")` 서브에이전트는 Fable 5로 실행된다 — Agent의 `model` 파라미터가 부모 모델보다 우선하기 때문. 세션 전체를 Fable로 켜는 것보다 훨씬 싸다.
  - 예외: `subagent_type: "fork"`는 항상 부모 모델을 상속하므로 fork에는 model 오버라이드가 안 먹는다. Fable을 쓰려면 일반 Agent(general-purpose 등)로 위임한다.
- Fable 서브에이전트는 **새 컨텍스트**로 시작한다. 위임 프롬프트에 배경·입력·기대 산출물을 명확히 담아야 한다(대화 전체를 자동으로 못 본다).
- Fable 5 제공기간이 끝났거나 호출이 오류나면, 자동으로 `Agent(model="opus", effort="max")`로 폴백하고 Park에게 알린다.
- 이 스킬은 "심층추론 티어"만 바꾼다. 위임 자체를 끄려면 CLAUDE.md "모델 오케스트레이션"의 `상태: OFF`를 쓴다(별개 스위치).
