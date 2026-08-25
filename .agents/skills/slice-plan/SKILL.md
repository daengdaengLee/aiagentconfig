---
name: slice-plan
description: Turns a design document or a settled requirement into a phased implementation plan where every phase is a vertical slice a human can actually touch — never a horizontal stack-order plan (migrations, then services, then API, then frontend). Writes to thoughts/plans/ with per-phase success criteria split into runnable automated commands and manual checks, plus an explicit out-of-scope section. Use when planning multi-file work — feature additions, refactors, migrations, upgrades — and whenever the user asks how to sequence the work — "구현 계획 세워줘", "어떤 순서로 작업할지 정하자", "페이즈 나눠줘", "작업 쪼개줘", "이거 어떻게 진행할지 계획 좀". Not for single-file edits or an obvious bug fix.
metadata:
  version: "1.0"
---

# 수직 슬라이스 구현 계획

설계를 실행 순서로 바꾼다. 결과물은 `thoughts/plans/` 에 놓이는 문서 하나이고,
`implement-plan` 이 그 문서만 보고 작업할 수 있을 만큼 구체적이어야 한다.

## 절대 규칙 — 수평 계획을 만들지 않는다

모델의 기본 성향은 스택 순서로 페이즈를 나누는 것이다.

```
1. 데이터베이스 마이그레이션
2. 서비스 계층
3. API
4. 프론트엔드
```

이렇게 나누면 **3번이 끝날 때까지 아무것도 만져볼 수 없다.**
AI 이전에는 500줄, 2000줄을 아무것도 확인하지 않고 쓰는 일이 드물었다.
테스트를 읽는 것은 시작일 뿐이고, 브라우저에서 띄워보거나 curl 로 찔러보는 것이
거의 모든 기능 개발의 일부였다. 그 확인 지점을 계획 단계에서 되살리는 것이 이 스킬의 목적이다.

각 페이즈는 **수직 슬라이스**여야 한다 — 얇게 관통해서 실제로 건드려볼 수 있는 단위.

### 판정 기준

페이즈를 하나 쓸 때마다 자문한다.

> **이 페이즈가 끝났을 때, 사람이 손으로 확인할 수 있는 것이 있는가?**

`curl` 로 응답을 받아볼 수 있거나, 브라우저에서 화면을 띄울 수 있거나,
CLI 명령을 실행해 결과를 볼 수 있어야 한다. "테스트가 통과한다" 만으로는 부족하다.
없으면 그 페이즈는 다시 쪼개거나 다른 페이즈와 합친다.

### 참고할 순서 — 가운데서 바깥으로

수직 슬라이스는 보통 이런 모양이 된다.

1. API 계약을 만들고 **가짜 데이터**를 반환한다 → `curl` 로 확인
2. 프론트엔드를 가짜 데이터에 연결한다 → 브라우저에서 다듬는다
3. 서비스 계층을 배선한다 (서비스가 아직 가짜 동작을 반환해도 된다)
4. 마이그레이션을 넣고 서비스를 실제 저장소에 연결한다
5. 비즈니스 로직을 채운다
6. 에러 처리를 채운다

각 단계마다 확인·수정할 지점이 있다. 이 순서를 그대로 쓰라는 뜻은 아니고,
"가운데서 시작해 바깥으로 나간다" 는 모양을 참고하라는 뜻이다.
작업 성격에 따라 다르므로, 슬라이스를 나눈 뒤에는 사용자에게 확인받는다.

## 진행 절차

### 1. 입력을 전부 읽는다

설계 문서(`thoughts/design/`)와 리서치 문서(`thoughts/research/`)가 있으면
부분 읽기 없이 전부 읽는다. 문서에서 지목한 코드 파일도 읽는다.

설계 문서가 없고 작업이 작지 않다면, 먼저 `design-spec` 을 돌리자고 제안한다.
설계 없이 세운 계획은 "동작은 하지만 엉뚱한 위치를 고치는" 결과로 가기 쉽다.

### 2. 페이즈 구조만 먼저 제시하고 승인받는다

상세를 쓰기 전에 목록만 보여준다.

```
## 개요
[한두 문장]

## 페이즈
1. [이름] — 이 페이즈가 끝나면 [사람이 확인할 수 있는 것]
2. [이름] — 이 페이즈가 끝나면 [사람이 확인할 수 있는 것]
3. ...

이 분할이 맞습니까? 순서나 크기를 조정할까요?
```

각 페이즈에 "끝나면 무엇을 확인할 수 있는지" 를 반드시 붙인다.
못 쓰는 페이즈가 있으면 그게 수평 계획이 섞였다는 신호다.

여기서 되돌리는 비용이 가장 싸다. 승인 없이 상세를 쓰지 않는다.

### 3. 상세 계획을 쓴다

`references/plan-template.md` 의 템플릿을 따른다. 세 가지를 빼지 않는다.

- **`## 하지 않을 것`** — 범위 밖을 명시한다. 범위가 새는 것을 막는 장치이자,
  리뷰어가 가장 먼저 읽게 되는 섹션이다
- **성공 기준을 자동/수동으로 분리** — 자동은 그대로 실행 가능한 명령 문자열,
  수동은 사람이 눈으로 봐야 하는 것. 이 구분이 없으면 구현 에이전트가
  "다 됐다" 고 선언하는 시점이 아무 의미도 갖지 못한다
- **파일 경로와 실제 코드 조각** — "인증 미들웨어를 수정한다" 는 계획이 아니다.
  어느 파일 어느 지점을 어떻게 바꾸는지 쓴다

### 4. 미해결 질문을 남기지 않는다

계획을 쓰다가 결정할 수 없는 지점이 나오면 **멈추고** 조사하거나 사용자에게 묻는다.
"구현하면서 정한다" 라고 적힌 계획은, 구현 에이전트가 그 자리에서 아무거나 고르게 만든다.
모든 결정은 계획이 확정되기 전에 끝나 있어야 한다.

### 5. 검토를 요청한다

문서 경로와 페이즈 목록을 보여주고 확인받는다.
계획 200줄을 읽는 것이 코드 2000줄을 읽는 것보다 훨씬 싸다는 게 이 문서의 존재 이유다.

## 다음 단계

계획이 확정되면 `implement-plan` 으로 넘어간다. 새 세션에서 시작하도록 안내한다.

## 공통 규약

- 산출물은 `thoughts/plans/YYYY-MM-DD-HHmm-description.md` 에 쓴다
- 문서 상단에 YAML 프론트매터를 둔다: `date`, `git_commit`, `branch`, `repository`, `topic`, `tags`, `status`
- 메타데이터는 저장소에 스크립트를 심지 않고 이 네 줄로 직접 수집한다

  ```bash
  date -Iseconds
  git rev-parse HEAD
  git branch --show-current
  basename "$(git rev-parse --show-toplevel)"
  ```

- 코드를 가리킬 때는 반드시 `file:line` 형식으로 쓴다
- 입력 문서와 언급된 파일은 부분 읽기 없이 전부 읽는다
- 플레이스홀더가 남은 문서는 쓰지 않는다
- 다음 단계(`implement-plan`)는 새 세션에서 시작하도록 안내한다. 이 문서가 컨텍스트 압축본 역할을 한다
