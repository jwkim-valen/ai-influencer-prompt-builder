# AI Influencer Prompt Builder — 사용 가이드

> 이 프로젝트는 Claude Code와 함께 사용하는 AI 인플루언서 이미지/영상 프롬프트 생성 도구입니다.
> 사용자가 원하는 장면을 설명하면, Claude가 자동으로 완성된 JSON 프롬프트를 생성하고 저장합니다.

---

## 목차

1. [프로젝트 구조](#1-프로젝트-구조)
2. [핵심 개념](#2-핵심-개념)
3. [시작하기 — 캐릭터 등록](#3-시작하기--캐릭터-등록)
4. [프롬프트 생성](#4-프롬프트-생성)
5. [Kling 영상 프롬프트](#5-kling-영상-프롬프트)
6. [품질 평가 시스템](#6-품질-평가-시스템)
7. [유틸리티 스킬](#7-유틸리티-스킬)
8. [파일 네이밍 규칙](#8-파일-네이밍-규칙)
9. [자주 쓰는 명령어](#9-자주-쓰는-명령어)
10. [주의사항](#10-주의사항)

---

## 1. 프로젝트 구조

```
Prompt Builder_v1
├── CLAUDE.md                    ← Claude가 자동으로 읽는 워크플로우 정의
├── GUIDE.md                     ← 이 파일
├── master_prompt.md             ← JSON 스키마, 공통 규칙
├── setup-prompt.md              ← 프로젝트 초기 세팅용
│
├── characters/                  ← 등록된 캐릭터 카드
│   ├── sofia.json
│   ├── yuna.json
│   └── jisoo.json
│
├── skills/                      ← Claude가 참조하는 기능 규칙
│   ├── ai-influencer.md         ← 카메라·조명·출력 포맷
│   ├── character-manager.md     ← 캐릭터 등록·로드·수정
│   ├── reference-image.md       ← 이미지 분석 규칙
│   ├── prompt-evaluator.md      ← 품질 평가 기준 (자동 실행)
│   ├── status.md                ← 프로젝트 현황 대시보드
│   ├── evaluate-all.md          ← 프롬프트 일괄 평가
│   ├── revise.md                ← 프롬프트 수정 + 재평가 통합
│   └── export.md                ← 프롬프트 내보내기
│
├── prompts/
│   ├── images/{character}/      ← 생성된 이미지 프롬프트 JSON
│   │   └── misc/                ← 미등록 캐릭터 프롬프트
│   └── video/{character}/       ← 생성된 Kling 영상 프롬프트
│       └── misc/
│
└── prompt_quality_log/          ← 프롬프트 품질 평가 기록
    ├── _summary.md              ← 전체 통계
    └── {character}.md           ← 캐릭터별 상세 기록
```

---

## 2. 핵심 개념

### 캐릭터 카드 (Character Card)
캐릭터의 외형을 정의하는 텍스트입니다. 한 번 등록하면 이후 모든 프롬프트에 그대로 복사되어 일관성을 유지합니다.

```
예시 — Jisoo:
"woman, early 20s, Korean fair skin tone with cool-neutral undertone,
 oval face with slightly elongated proportions and natural asymmetry, ..."
```

### JSON 프롬프트 구조
생성되는 프롬프트는 아래 구조를 따릅니다:

```json
{
  "prompt": "씬 전체를 서술하는 텍스트",
  "negative_prompt": "생성하지 말아야 할 요소",
  "settings": {
    "resolution", "aspect_ratio", "style",
    "lighting", "camera", "image_quality_simulation",
    "explicit_restrictions", "color_grading"
  }
}
```

### UGC 스타일이란?
이 프로젝트의 모든 프롬프트는 **실제 사람이 폰으로 찍은 것처럼** 보이는 이미지를 목표로 합니다.
AI 생성 느낌, 과도한 보정, 스튜디오 촬영 느낌을 철저히 배제합니다.

---

## 3. 시작하기 — 캐릭터 등록

캐릭터를 먼저 등록해야 프롬프트를 만들 수 있습니다.

### 방법 A — 이미지로 등록 (권장)
인물 사진을 첨부하면 Claude가 외형을 자동 분석합니다.

```
사용자: [이미지 첨부] 캐릭터 생성
Claude: 분석 완료. character card 초안입니다: ...
사용자: (수정 없으면) 저장
Claude: 이름을 알려주세요.
사용자: yuna
Claude: 저장 완료.
```

### 방법 B — 텍스트로 등록
```
사용자: 새 캐릭터 추가해줘.
        20대 초반 한국인 여성, 웜톤 피부, 긴 생머리...
Claude: 이렇게 저장할까요? [카드 초안 출력]
사용자: 저장
```

### 등록 시 자동 생성되는 것
캐릭터 등록이 완료되면 아래가 함께 만들어집니다:
- `characters/{id}.json`
- `prompts/images/{id}/` 폴더
- `prompts/video/{id}/` 폴더
- `prompt_quality_log/{id}.md`

### 등록된 캐릭터 확인
```
사용자: 캐릭터 목록 보여줘
```

---

## 4. 프롬프트 생성

### 기본 흐름

```
1. 캐릭터 지정 (이름 언급 또는 목록에서 선택)
2. 씬 정보 제공 (장소, 포즈, 제품, 의상)
3. Claude가 JSON 생성 및 저장
4. 품질 평가 자동 실행
5. 수정 여부 확인
```

### 레퍼런스 이미지 활용

**포즈 참고용 (기존 캐릭터)**
```
사용자: [이미지 첨부] Jisoo 프롬프트 만들어줘
→ Claude가 이미지에서 포즈·구도·조명·무드를 추출하여 프롬프트에 반영
```

**제품 이미지 첨부**
```
사용자: [제품 이미지 첨부] Jisoo 이 제품 홍보 이미지
→ Claude가 제품 형태·라벨·색상을 분석하여 프롬프트에 반영
```

**동시 첨부도 가능**
```
사용자: [포즈 이미지 + 제품 이미지] Jisoo 프롬프트 생성
```

### 씬 정보 예시

| 항목 | 예시 |
|---|---|
| 장소 | 한강 산책로, 카페 실내, 스튜디오, 편의점 앞 |
| 포즈 | 셀피, 제품 들고 서있기, 바닥에 앉아서 |
| 제품 | 샴푸 병, 립 제품, 운동화, 텀블러 |
| 의상 | 오버사이즈 티셔츠 + 미니스커트, 운동복 |
| 카메라 | 셀피(전면) / 후면 카메라 |

### 저장 경로

| 캐릭터 유형 | 경로 |
|---|---|
| 등록된 캐릭터 | `prompts/images/{character}/{character}-{location}-{product}-{nn}.json` |
| 미등록 캐릭터 | `prompts/images/misc/new-{location}-{product}-{nn}.json` |

---

## 5. Kling 영상 프롬프트

이미지 프롬프트 확정 후 Kling 애니메이션 프롬프트를 추가로 생성할 수 있습니다.

```
사용자: Kling 애니메이션 프롬프트 작성해줘
Claude: [Kling 텍스트 프롬프트 생성 및 저장]
```

저장 경로: `prompts/video/{character}/{character}-{location}-{product}-{nn}-kling.txt`

**Kling 프롬프트 특징**
- 미세한 동작 위주 (호흡, 머리카락 흔들림, 눈 깜빡임)
- 카메라는 기본 static (필요시 slow push-in)
- 3–5초 분량, loop-friendly 엔딩

---

## 6. 품질 평가 시스템

프롬프트 생성 직후 자동으로 5점 만점 평가가 실행됩니다.

### 평가 기준

| 항목 | 내용 |
|---|---|
| 캐릭터 충실도 | character card가 원문 그대로 반영됐는가 |
| 씬/포즈 명확도 | 포즈·구도·표정·카메라가 구체적으로 기술됐는가 |
| 제품/의상 디테일 | 형태·색상·라벨이 정확히 기술됐는가 |
| 조명·무드 일관성 | 조명 서술과 settings 값이 일치하는가 |
| 네거티브 특이성 | 씬별 발생하기 쉬운 오류를 추가 차단했는가 |

### 평가 기록 확인

```
사용자: Jisoo 폴더 내 프롬프트에 대한 평가항목 나열
→ prompt_quality_log/jisoo.md 내용 출력

사용자: 전체 현황 보여줘
→ prompt_quality_log/_summary.md 내용 출력
```

### 평가 점수 업데이트
실제 생성 결과를 보고 점수를 수정할 수 있습니다:
```
사용자: jisoo-studio-hinok-01.json 네거티브 특이성 별로야. 손이 여러개 생성돼
→ Claude가 negative_prompt 수정 + 점수 재기록
```

---

## 7. 유틸리티 스킬

반복 작업을 자동화하는 스킬입니다. 트리거 문구를 입력하면 실행됩니다.

### `/status` — 프로젝트 현황 대시보드
캐릭터별 프롬프트 수, 품질 점수, 미평가 항목을 한눈에 표시합니다.
```
사용자: status  /  현황 보여줘  /  프로젝트 현황
```

### `/evaluate-all {character}` — 일괄 평가
특정 캐릭터(또는 전체)의 프롬프트를 한 번에 평가하고 log를 업데이트합니다.
이미 평가된 파일은 자동 스킵. `"전체 재평가"`로 강제 덮어쓰기 가능.
```
사용자: evaluate-all jisoo  /  jisoo 전체 평가해줘  /  전체 프롬프트 평가
```

### `/revise {filename}` — 수정 + 재평가 통합
프롬프트 수정 → 저장 → 재평가 → log 업데이트를 한 번에 처리합니다.
연결된 Kling 프롬프트가 있으면 함께 수정 여부를 묻습니다.
```
사용자: revise jisoo-studio-hinok-01.json  /  {파일명} 수정해줘
```

### `/export {character}` — 프롬프트 내보내기
캐릭터의 프롬프트 목록을 원하는 형식으로 정리해서 출력합니다.

| 포맷 | 설명 |
|---|---|
| `summary` (기본) | 파일명 + prompt 첫 줄 + 총점 |
| `prompt-only` | prompt 텍스트만 나열 |
| `full` | JSON 전문 |
| `csv` | 파일명, 점수, 메모를 CSV 형태로 |

```
사용자: export jisoo  /  jisoo 프롬프트 내보내줘  /  jisoo export csv
```

---

## 8. 파일 네이밍 규칙

```
{character}-{location}-{product}-{nn}.json

예시:
jisoo-studio-hinok-01.json      ← Jisoo / 스튜디오 / hinok 샴푸 / 첫 번째
yuna-hangang-bink-01.json       ← Yuna / 한강 / Bink 텀블러 / 첫 번째
sofia-cafe-prime-01.json        ← Sofia / 카페 / Prime / 첫 번째

미등록 캐릭터:
new-dark-muzgae-01.json         ← 미등록 / 다크 배경 / Muzgae 제품 / 첫 번째
```

---

## 9. 자주 쓰는 명령어

### 캐릭터 관리
| 명령어 | 동작 |
|---|---|
| `[이미지] 캐릭터 생성` | 이미지 분석 후 신규 캐릭터 등록 |
| `캐릭터 목록 보여줘` | 등록된 캐릭터 전체 출력 |
| `{이름} 카드 수정해줘` | 캐릭터 카드 수정 |

### 프롬프트 생성
| 명령어 | 동작 |
|---|---|
| `{이름} 프롬프트 만들어줘` | 해당 캐릭터로 프롬프트 생성 시작 |
| `[이미지] {이름} 프롬프트 생성` | 이미지 포즈 참고하여 프롬프트 생성 |
| `Kling 애니메이션 프롬프트 작성해줘` | 확정된 이미지 기반 Kling 프롬프트 생성 |

### 유틸리티 스킬
| 명령어 | 동작 |
|---|---|
| `status` | 프로젝트 전체 현황 대시보드 출력 |
| `evaluate-all {이름}` | 캐릭터 프롬프트 일괄 평가 |
| `revise {파일명}` | 프롬프트 수정 + 재평가 통합 실행 |
| `export {이름}` | 프롬프트 목록 내보내기 |
| `{이름} 폴더 내 프롬프트 평가항목 나열` | 캐릭터별 품질 평가 기록 출력 |

---

## 10. 주의사항

- **셀피에 배경 블러 없음** — 전면 카메라 셀피에는 bokeh/depth of field 절대 사용 안 함
- **캐릭터 카드 임의 수정 금지** — 저장된 card 텍스트를 paraphrase하거나 변형하면 일관성 깨짐
- **제품 디테일 임의 생성 금지** — 모르는 브랜드 정보는 추측하지 않음. 이미지 첨부 필요
- **신규 캐릭터 확인 후 저장** — 카드 초안을 반드시 확인한 뒤 저장 명령을 내릴 것
- **양손 포즈 시 extra hands 차단** — negative_prompt에 `three hands, extra hands` 계열 추가 필요
- **revise 후 Kling 연동 확인** — 이미지 프롬프트 수정 시 Kling 프롬프트도 함께 업데이트 필요
