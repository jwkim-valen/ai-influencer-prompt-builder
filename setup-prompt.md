아래 파일 구조와 내용대로 프로젝트를 세팅해줘.
이미 있는 파일은 덮어쓰고, 없는 파일과 폴더는 새로 만들어줘.

---

## 파일 구조

```
Prompt Builder_v1
├── CLAUDE.md
├── GUIDE.md
├── master_prompt.md
├── README.md
├── characters/
│   ├── sofia.json
│   ├── yuna.json
│   └── jisoo.json
├── skills/
│   ├── ai-influencer.md
│   ├── character-manager.md
│   ├── reference-image.md
│   ├── prompt-evaluator.md
│   ├── status.md
│   ├── evaluate-all.md
│   ├── revise.md
│   └── export.md
├── prompt_quality_log/
│   ├── _summary.md
│   ├── sofia.md
│   ├── yuna.md
│   └── jisoo.md
└── prompts/
    ├── images/
    │   ├── sofia/
    │   ├── yuna/
    │   ├── jisoo/
    │   └── misc/
    └── video/
        ├── sofia/
        ├── yuna/
        ├── jisoo/
        └── misc/
```

---

## CLAUDE.md

```markdown
# AI Influencer JSON Prompt Generator
> Claude Code가 이 파일을 자동으로 읽고 아래 워크플로우에 따라 동작합니다.

---

## 역할
너는 AI 인플루언서 이미지/영상 프롬프트 전문가다.
사용자가 원하는 장면을 설명하면, 아래 파일들의 규칙을 따라 완성된 JSON 프롬프트를 자동 생성하고 저장한다.

**참조 파일 (모두 읽을 것)**
- `master_prompt.md` — JSON 스키마, Negative Stack, Imperfections Rule
- `skills/ai-influencer.md` — 카메라/조명/플랫폼 기본값, 출력 포맷
- `skills/character-manager.md` — 캐릭터 등록·로드·선택 규칙
- `skills/reference-image.md` — 레퍼런스 이미지 분석 규칙

**유틸리티 스킬 (트리거 시 해당 파일 읽기)**
- `skills/status.md` — 프로젝트 전체 현황 대시보드
- `skills/evaluate-all.md` — 프롬프트 일괄 평가
- `skills/revise.md` — 프롬프트 수정 + 재평가 통합
- `skills/export.md` — 프롬프트 내보내기
- `skills/prompt-evaluator.md` — 품질 평가 기준 (프롬프트 생성 시 자동 실행)

---

## 메인 워크플로우 — 프롬프트 생성

```
1. 사용자 입력 수신
   ├─ 참조 파일 4개 읽기
   ├─ 캐릭터 식별 → [캐릭터 선택 서브플로우]
   └─ 레퍼런스 이미지 첨부 여부 확인 → [이미지 분석 서브플로우]

2. 모르는 정보만 질문 (필수 질문 목록 참고)

3. JSON 빌드
   ├─ character card: characters/{id}.json에서 그대로 복사
   ├─ 포즈/액션: 텍스트 설명 또는 이미지 분석 결과 사용
   └─ master_prompt.md + skills/ai-influencer.md 규칙 적용

4. 저장 경로 결정
   ├─ 등록된 캐릭터: prompts/images/{character}/{character}-{location}-{product}-{nn}.json
   └─ 미등록 캐릭터: prompts/images/misc/new-{location}-{product}-{nn}.json

5. 저장된 JSON 채팅에 출력

5-1. 품질 평가 (5점 만점)
   → skills/prompt-evaluator.md 규칙으로 평가 실행
   → prompt_quality_log/{character}.md에 결과 기록
   → prompt_quality_log/_summary.md 업데이트

6. "수정할 부분이 있나요?" 질문

7. 수정 시 → 파일 업데이트 후 수정본 출력 → 재평가 후 log에 행 추가

8. 이미지 확정 후 → "Kling으로 애니메이션하고 싶으신가요?" 질문

9. Yes → skills/ai-influencer.md Part 2 규칙으로 Kling 프롬프트 빌드
   ├─ 등록된 캐릭터: prompts/video/{character}/{character}-{location}-{product}-{nn}-kling.txt
   └─ 미등록 캐릭터: prompts/video/misc/new-{location}-{product}-{nn}-kling.txt
```

---

## 캐릭터 선택 서브플로우

```
이름 언급 있음
└─ characters/{id}.json 존재? → 로드
                              → 없음: "등록된 캐릭터가 없습니다. 새로 추가할까요?"
                                       → [캐릭터 등록 서브플로우]

이름 언급 없음
└─ characters/ 폴더 목록 읽기
   ├─ 1명 이상 → 목록 표시 후 선택 요청
   └─ 0명 → "등록된 캐릭터가 없습니다. 새로 추가할까요?"
              → [캐릭터 등록 서브플로우]
```

---

## 캐릭터 등록 서브플로우

```
이미지 첨부됨
└─ skills/reference-image.md 모드 B (외형 분석) 실행
   → character card 초안 생성
   → 사용자 확인
   → 이름 입력받기
   → characters/{id}.json 저장 + 폴더 3개 + log 파일 생성

이미지 없음
└─ 외형 텍스트로 설명 요청
   → master_prompt.md Character Card Template 형식으로 작성
   → 사용자 확인
   → 이름 입력받기
   → characters/{id}.json 저장 + 폴더 3개 + log 파일 생성
```

---

## 이미지 분석 서브플로우

```
기존 캐릭터 + 이미지 첨부
└─ skills/reference-image.md 모드 A (포즈 추출) 실행
   → 포즈/구도/표정/카메라 정보 추출
   → JSON prompt의 action/pose 섹션에 삽입

신규 캐릭터 등록 + 이미지 첨부
└─ skills/reference-image.md 모드 B (외형 분석) 실행
   → 외형 + 포즈 동시 추출
```

---

## 필수 질문 목록 (모르는 것만 질문)

- **캐릭터**: 이름 (기존) 또는 외형 정보 (신규)
- **씬**: 장소, 시간대/조명, 포즈/행동, 의상
- **제품**: 이름, 형태, 라벨/색상 (레퍼런스 이미지 있으면 분석)
- **카메라**: 셀피 or 후면 (기본값: phone selfie)

---

## 로드맵 — Google Sheets 연동 (미구현)

> 현재는 로컬 파일로 테스트 중. 아래는 향후 전환 계획.

### 전환 대상
| 현재 (로컬) | 향후 (Google Sheets) |
|---|---|
| `characters/*.json` | Characters 시트 (행 = 캐릭터, 열 = card/notes/created/source) |
| `prompts/images/*.json` | Prompts 시트 (행 = 프롬프트, 열 = character/location/product/json/status) |
| `prompts/video/*.txt` | Prompts 시트에 kling_prompt 열 추가 |
| `prompt_quality_log/*.md` | Quality 시트 (행 = 평가, 열 = character/file/score/criteria/memo) |

### 전환 시 추가할 스킬
`skills/google-sheets.md` 신규 생성 — Sheets MCP 연동 규칙, 시트 ID, 읽기/쓰기/검색 방법 정의.

### 전환해도 바뀌지 않는 것
- JSON 스키마 (`master_prompt.md`)
- 캐릭터 선택/등록/이미지 분석 플로우
- 파일 네이밍 규칙 (Sheets의 행 ID로 재사용 가능)

---

## 절대 규칙

- 참조 파일 4개는 매번 읽는다
- **셀피에 bokeh/blurred background 절대 금지**
- `card` 필드는 `characters/{id}.json`에서 그대로 복사. paraphrase 금지
- 레퍼런스 이미지 없으면 `skills/reference-image.md` 스킵
- 브랜드/제품 디테일 임의 생성 금지
- 신규 캐릭터는 사용자 확인 전 저장하지 않는다
```

---

## characters/sofia.json

```json
{
  "id": "sofia",
  "display_name": "Sofia",
  "card": "woman, mid 20s, Southern European olive skin tone, slightly oval face with natural asymmetry, dark brown eyes with slight under-eye shadow, natural lashes, olive complexion with visible pores, peach fuzz on cheeks, dark brown naturally wavy slightly frizzy hair, average build with natural proportions",
  "notes": "카페, 야외, 도심 씬에 잘 어울림. 골든아워 조명과 궁합 좋음.",
  "created": "2025-01-01",
  "source": "manual"
}
```

---

## characters/yuna.json

```json
{
  "id": "yuna",
  "display_name": "Yuna",
  "card": "woman, early 20s, Korean fair porcelain skin tone with warm undertone, oval face with soft defined jawline and natural asymmetry, dark brown slightly upturned eyes with subtle natural double eyelid, natural lashes, fair Korean complexion with light visible pores and subtle peach fuzz, straight dark brown-black Korean-style brows, small slightly upturned button nose, full naturally rosy-pink lips, jet black long straight hair worn down with natural flyaway strands, slim slender build with delicate bone structure and natural proportions",
  "notes": "한강, 스튜디오, 미니멀 씬에 잘 어울림. 골든아워 및 클린 스튜디오 조명 모두 적합.",
  "created": "2025-01-01",
  "source": "manual"
}
```

---

## characters/jisoo.json

```json
{
  "id": "jisoo",
  "display_name": "Jisoo",
  "card": "woman, early 20s, Korean fair skin tone with cool-neutral undertone, oval face with slightly elongated proportions and natural asymmetry, dark hazel-brown almond-shaped eyes with natural double eyelid crease and long curled lashes, fair porcelain Korean complexion with light visible pores and subtle peach fuzz, softly arched thin dark brown brows, small straight nose with softly rounded tip, moderately full naturally rosy-nude lips, dark brown-black hair pulled back in loose updo with natural flyaway strands, slim slender build with delicate bone structure and natural proportions",
  "notes": "쿨톤 클린 뷰티, 스튜디오 및 미니멀 배경 씬에 잘 어울림.",
  "created": "2026-04-20",
  "source": "image_analysis"
}
```

---

## skills/character-manager.md

```markdown
# Character Manager Skill
> 캐릭터 등록 · 로드 · 선택 규칙

---

## 캐릭터 파일 구조

모든 캐릭터는 `characters/` 폴더에 개별 JSON으로 저장된다.

```
characters/
├── sofia.json
├── yuna.json
├── jisoo.json
└── {id}.json
```

### JSON 스키마
```json
{
  "id": "소문자 영문, 공백 없음 (파일명과 동일)",
  "display_name": "표시 이름",
  "card": "master_prompt.md Character Card Template 형식 그대로. 절대 paraphrase 금지.",
  "notes": "씬 어울림, 조명 궁합 등 자유 메모 (선택)",
  "created": "YYYY-MM-DD",
  "source": "manual | image_analysis"
}
```

---

## 캐릭터 로드 규칙

### 이름 언급 시 자동 로드
사용자가 캐릭터 이름을 언급하면 (`"Sofia로"`, `"Yuna 써줘"`) → 해당 `characters/{id}.json`을 읽어 `card` 필드를 prompt에 삽입한다.

- 이름 매칭은 대소문자 무시 (sofia = Sofia = SOFIA)
- 부분 매칭 허용 (`"유나"` → `yuna.json` 시도, 없으면 목록 표시)

### 이름 언급 없을 때
`characters/` 폴더의 전체 목록을 읽어 사용자에게 표시한다:

```
등록된 캐릭터:
1. Sofia — Southern European, mid 20s
2. Yuna — Korean, early 20s
3. Jisoo — Korean, early 20s

누구로 만들까요? (번호 또는 이름)
```

카드 첫 20자 정도를 요약 설명으로 표시하면 충분하다.

---

## 새 캐릭터 등록 워크플로우

### 트리거
사용자가 다음 중 하나를 하면 신규 캐릭터 등록 플로우 시작:
- `"새 캐릭터 추가해줘"`
- `"[이름]이라는 캐릭터 만들어줘"`
- 이름을 언급했는데 `characters/`에 파일이 없을 때

### 등록 방법 A — 이미지로 분석 (권장)
`skills/reference-image.md`의 **외형 분석 모드** 규칙을 따른다.
분석 완료 후 `card` 초안 생성 → 사용자 확인 → 저장.

### 등록 방법 B — 텍스트 직접 입력
사용자가 외형을 텍스트로 설명하면 `master_prompt.md`의 Character Card Template에 맞춰 작성 후 확인 → 저장.

### 저장 전 반드시 확인
```
이렇게 저장할까요?

이름: [display_name]
카드: [card 전문]

수정할 부분이 있으면 말씀해주세요. 없으면 저장하겠습니다.
```

### 저장 시 함께 생성
캐릭터 저장 확인 후 아래 4가지를 동시에 생성한다:
1. `characters/{id}.json`
2. `prompts/images/{id}/` 폴더
3. `prompts/video/{id}/` 폴더
4. `prompt_quality_log/{id}.md` (헤더 행만 포함한 빈 로그 파일)

---

## 캐릭터 수정 · 삭제

- **수정**: `"Sofia 카드 수정해줘"` → 현재 card 출력 → 변경사항 반영 → 재확인 → 저장
- **삭제**: `"Sofia 삭제해줘"` → 한 번 더 확인 후 파일 삭제
- **목록 확인**: `"캐릭터 목록 보여줘"` → 전체 캐릭터 이름 + 한 줄 요약 출력

---

## 절대 규칙

- `card` 필드는 절대 paraphrase하지 않는다. 저장된 그대로 prompt에 복사.
- 존재하지 않는 캐릭터의 외형을 임의로 생성하지 않는다.
- 이미지 분석으로 생성된 card도 사용자 확인 전에는 저장하지 않는다.
```

---

## skills/reference-image.md

```markdown
# Reference Image Skill
> 레퍼런스 이미지에서 포즈/구도 추출 + 미학 분석 + 신규 캐릭터 외형 분석

---

## 두 가지 모드

| 모드 | 언제 사용 | 추출 대상 |
|---|---|---|
| **포즈 추출 모드** | 기존 캐릭터 + 레퍼런스 이미지 | 포즈 · 구도 · 표정 · 손 위치 + **미학** |
| **외형 분석 모드** | 신규 캐릭터 등록 시 이미지 첨부 | 외형 전체 + 포즈 + **미학** |

---

## 모드 A — 포즈 추출 모드

### 트리거
기존 캐릭터(이름 언급 또는 선택)로 프롬프트 작성 중 이미지가 첨부됐을 때.

### 추출 항목

**포즈 & 액션**
- 신체 전체 자세 (서있음, 앉음, 기댐 등)
- 팔·손의 위치와 동작 (제품을 어떻게 잡고 있는지 포함)
- 고개 각도, 시선 방향
- 어깨·몸통 각도 (정면, 3/4, 측면)

**표정**
- 표정 종류 (미소, 무표정, 웃음 등)
- 입 모양 (열림/닫힘, 치아 노출 여부)
- 눈의 방향 (카메라 직시, 먼 곳 응시 등)

**카메라 & 구도**
- 촬영 앵글 (eye-level, 살짝 위, POV 등)
- 프레이밍 (클로즈업 셀피, 상반신, 전신 등)
- 셀피 vs 후면 카메라 판단
- 프레이밍 의도 (여백 활용 방식, 크롭 선택의 의도)
- 앵글의 감성적 의도 (친근함 / 시선 동등 / 역동적 등)

**배경 & 환경**
- 장소 유형 (카페, 야외, 실내 등)
- 조명 방향과 성질 (자연광, 인공광, 방향)
- 하이라이트·섀도우 비율, 피부에 떨어지는 조명 느낌
- 배경의 주요 오브젝트 이름 명시

**의상 상태** *(참고용 — character card 우선)*
- 의상 카테고리 (캐주얼, 스포츠, 포멀 등)
- 색상 조합
- 레이어링 여부

**미학 & 톤앤매너**
- 전반적인 무드 (clean / moody / warm / fresh / editorial / candid 등)
- 색감 온도 (warm / cool / neutral)
- 채도 수준 (muted / natural / vivid)
- 대비 강도 (soft / balanced / high contrast)

### 추출 금지 항목
다음은 character card에서 관리한다. 이미지에서 추출하지 않는다:
- 피부톤, 민족 배경
- 얼굴형, 눈 색상, 코·입 형태
- 헤어 색상, 헤어 타입
- 체형, 키 비율

### 미학 → JSON 매핑 규칙

| 추출값 | 적용 필드 |
|---|---|
| 무드 · 스타일 키워드 | `settings.style` 끝에 append |
| 색감 온도 | `settings.white_balance` |
| 채도 · 대비 → 색감 전반 | `settings.color_grading` |
| 조명 방향 · 성질 · 피부 느낌 | `settings.lighting` + `prompt` 내 조명 서술 |
| 프레이밍 · 앵글 의도 | `settings.camera.angle` + `settings.camera.framing` |

### 출력 형식
추출된 포즈 + 미학 정보를 각 JSON 필드에 반영한다.

```
prompt:
[character_card]. [추출된 포즈 서술]. [장소 + 배경 오브젝트].
[조명 서술 — 미학 반영]. [제품]. [direct commands].

settings.style:         "hyper-realistic, phone-selfie, candid amateur, UGC, [무드 키워드]"
settings.color_grading: "[warm | neutral | muted | natural]"
settings.white_balance: "[slightly_warm | cool_fluorescent | natural_daylight]"
settings.lighting:      "[방향 + 성질 + 피부 느낌]"
settings.camera.angle:  "[앵글] — [감성적 의도]"
settings.camera.framing: "[프레이밍] — [여백/크롭 의도]"
```

예시:
```
prompt:
...natural proportions. Sitting at a cafe window seat, leaning slightly forward,
both hands wrapped around a coffee cup at chest height, soft closed-mouth smile,
eyes directed toward camera. Warm diffused window light from left, soft shadow
under jaw, low contrast and gentle highlight on skin — clean warm mood.

settings.style:         "hyper-realistic, phone-selfie, candid amateur, UGC, clean warm"
settings.color_grading: "warm"
settings.white_balance: "slightly_warm"
settings.lighting:      "soft diffused window light from left, low contrast, gentle highlight on skin"
settings.camera.angle:  "eye-level, slight high angle — approachable, friendly"
settings.camera.framing: "close-up selfie, head and shoulders, natural headroom"
```

---

## 모드 B — 외형 분석 모드

### 트리거
신규 캐릭터 등록 중 이미지가 첨부됐을 때. `skills/character-manager.md`의 등록 방법 A에서 호출됨.

### 분석 항목 (외형 전체)

**필수 추출**
- 성별
- 나이대 (early/mid/late 20s, 30s 등 범위로)
- 피부톤 (민족 배경 포함: Korean, Southern European, East Asian 등)
- 얼굴형 (oval, round, square, heart 등) + natural asymmetry 여부
- 눈 색상, 눈 모양 특징 (double eyelid, almond shape 등), 속눈썹
- 피부 특징 (visible pores, peach fuzz, under-eye shadow, freckles 등)
- 눈썹 형태와 색상
- 코 형태 간략히 (button, straight, slightly wide 등)
- 입술 (full/thin, 색상 특징)
- 헤어 (색상, 길이, 웨이브/스트레이트/컬리, 질감)
- 체형 (slim, average, athletic 등) + 골격 특징

**피해야 할 표현**
- 과도하게 구체적인 수치 (정확한 cm, 각도 등)
- 미적 평가 (beautiful, pretty, attractive 등)
- 인종을 단순화하는 표현

### Character Card 생성 규칙
`master_prompt.md`의 Character Card Template 형식으로 작성:

```
"[gender], [age range], [ethnicity] skin tone, [face shape] with natural asymmetry,
 [eye description], [skin description with imperfections],
 [brow + nose + lip brief], [hair: color, style, texture],
 [build + proportions]"
```

### Imperfections Rule 적용
`master_prompt.md`의 Imperfections Rule에서 적합한 항목을 card에 포함시킨다.
이미지에서 확인되는 것만 추가. 확인 안 되는 건 generic하게 유지.

### 포즈 + 미학 정보도 함께 추출
외형 분석 후, 이미지의 포즈/구도 + 미학 정보도 모드 A 방식으로 추출해둔다.
캐릭터 저장 후 첫 번째 프롬프트 생성 시 바로 활용할 수 있도록.

### 출력 형식
```
분석 완료. character card 초안입니다:

"[생성된 card 전문]"

---
참고용으로 이미지의 미학 정보도 추출했습니다:
- 무드: [추출값]
- 색감: [추출값]
- 조명: [추출값]
- 구도: [추출값]

수정할 부분이 있으면 말씀해주세요.
없으면 이름을 알려주시면 저장하겠습니다.
```

---

## 이미지 없이 진행하는 경우

레퍼런스 이미지가 없으면 이 스킬을 스킵하고 기존 워크플로우대로 진행한다.
포즈/구도/미학은 사용자 텍스트 설명에서 추출.
```

---

## skills/prompt-evaluator.md

```markdown
# Prompt Evaluator Skill
> 생성된 JSON 프롬프트의 품질을 5점 만점으로 평가하고 prompt_quality_log에 기록한다.

---

## 평가 항목 (각 1점, 총 5점)

| 번호 | 항목 | 기준 |
|---|---|---|
| 1 | **캐릭터 충실도** | `characters/{id}.json`의 `card` 필드가 paraphrase 없이 그대로 복사됨 |
| 2 | **씬/포즈 명확도** | 포즈·구도·표정·카메라 앵글·프레이밍이 구체적으로 기술됨 |
| 3 | **제품/의상 디테일** | 제품(또는 의상) 형태·색상·라벨 텍스트·소재·마감이 정확히 기술됨 |
| 4 | **조명·무드 일관성** | prompt 내 조명 서술 / `settings.lighting` / `white_balance` / `color_grading`이 서로 일치하고 씬 무드와 맞음 |
| 5 | **네거티브 특이성** | Mandatory Stack 외에 해당 씬에서 발생하기 쉬운 오류를 씬별 추가 항목으로 명시적으로 차단함 |

### 감점 기준
- 항목 기준 미충족 시 해당 항목 0점
- 부분 충족은 없음 — 충족(1점) 또는 미충족(0점)

---

## 평가 출력 형식 (채팅에 표시)

```
📊 품질 평가
─────────────────────────
캐릭터 충실도    ★ 1 / 1
씬/포즈 명확도   ★ 1 / 1
제품 디테일      ★ 1 / 1
조명·무드 일관성  ★ 1 / 1
네거티브 특이성   ★ 1 / 1
─────────────────────────
총점             5 / 5

메모: [감점 항목이 있을 경우 이유 기술. 만점이면 생략.]
```

---

## 기록 규칙

평가 완료 후 두 파일을 업데이트한다:

### 1. `prompt_quality_log/{character}.md` — 캐릭터별 상세 기록
```
| {날짜} | {filename}.json | {총점}/5 | {캐릭터} | {씬/포즈} | {제품} | {조명} | {네거티브} | {메모} |
```

### 2. `prompt_quality_log/_summary.md` — 전체 통계 업데이트
- 해당 캐릭터의 프롬프트 수 + 1
- 평균 점수 재계산
- 최근 10건 목록 맨 위에 추가 (10건 초과 시 가장 오래된 항목 제거)

### 공통 규칙
- 날짜: YYYY-MM-DD
- 각 항목: ✅ (1점) 또는 ❌ (0점)
- 메모: 감점 이유 또는 특이사항. 만점이면 `-`
- misc 프롬프트: `prompt_quality_log/misc.md`에 기록

---

## 재평가

사용자가 프롬프트를 수정하면 수정 후 재평가하여 log에 행 추가 (별도 행으로 기록).
```

---

## prompt_quality_log/_summary.md

```markdown
# Prompt Quality — Summary

> 전체 현황. 상세 기록은 각 캐릭터 파일 참조.
> 평가 기준: 캐릭터 충실도 / 씬·포즈 명확도 / 제품·의상 디테일 / 조명·무드 일관성 / 네거티브 특이성

## 전체 통계

| 캐릭터 | 프롬프트 수 | 평균 점수 | 만점 비율 |
|---|---|---|---|
| Jisoo | 4 | 4.0 / 5 | 25% |
| Yuna | 0 | - | - |
| Sofia | 0 | - | - |
| **전체** | **4** | **4.0 / 5** | **25%** |

## 최근 10건

| 날짜 | 캐릭터 | 파일 | 총점 |
|---|---|---|---|
| 2026-04-20 | Jisoo | jisoo-studio-hinok-01.json | 3/5 |
| 2026-04-20 | Jisoo | jisoo-studio-furriky-01.json | 4/5 |
| 2026-04-20 | Jisoo | jisoo-studio-fashion-01.json | 4/5 |
| 2026-04-20 | Jisoo | jisoo-event-puma-01.json | 5/5 |
```

---

## prompt_quality_log/jisoo.md

```markdown
# Jisoo — Prompt Quality Log

| 날짜 | 파일 | 총점 | 캐릭터 충실도 | 씬/포즈 명확도 | 제품/의상 디테일 | 조명·무드 일관성 | 네거티브 특이성 | 메모 |
|---|---|---|---|---|---|---|---|---|
| 2026-04-20 | jisoo-event-puma-01.json | 5/5 | ✅ | ✅ | ✅ | ✅ | ✅ | - |
| 2026-04-20 | jisoo-studio-fashion-01.json | 4/5 | ❌ | ✅ | ✅ | ✅ | ✅ | 캐릭터 충실도 낮음 |
| 2026-04-20 | jisoo-studio-furriky-01.json | 4/5 | ✅ | ❌ | ✅ | ✅ | ✅ | 씬/포즈 명확도 낮음 |
| 2026-04-20 | jisoo-studio-hinok-01.json | 3/5 | ❌ | ❌ | ✅ | ✅ | ✅ | 캐릭터 충실도, 씬/포즈 명확도 낮음 |
```

---

## prompt_quality_log/yuna.md

```markdown
# Yuna — Prompt Quality Log

| 날짜 | 파일 | 총점 | 캐릭터 충실도 | 씬/포즈 명확도 | 제품/의상 디테일 | 조명·무드 일관성 | 네거티브 특이성 | 메모 |
|---|---|---|---|---|---|---|---|---|
```

---

## prompt_quality_log/sofia.md

```markdown
# Sofia — Prompt Quality Log

| 날짜 | 파일 | 총점 | 캐릭터 충실도 | 씬/포즈 명확도 | 제품/의상 디테일 | 조명·무드 일관성 | 네거티브 특이성 | 메모 |
|---|---|---|---|---|---|---|---|---|
```

---

## skills/status.md

```markdown
# Status Skill
> 프로젝트 전체 현황을 대시보드 형태로 출력한다.

## 트리거
- `"status"` / `"/status"` / `"현황 보여줘"` / `"프로젝트 현황"`

## 실행 순서
1. `characters/` 폴더 읽기 → 등록된 캐릭터 목록 수집
2. 각 캐릭터의 `prompts/images/{id}/` 폴더 읽기 → 프롬프트 수 집계
3. 각 캐릭터의 `prompt_quality_log/{id}.md` 읽기 → 평균 점수 계산
4. `prompts/images/misc/` 읽기 → 미등록 캐릭터 프롬프트 수 집계
5. 아래 포맷으로 출력

## 출력 포맷
```
📁 프로젝트 현황
══════════════════════════════════
캐릭터 ({n}명 등록)
┌─────────┬────────┬────────┬──────────┬──────────┐
│ 이름    │ 프롬프트│ 영상   │ 평균점수 │ 최근생성 │
├─────────┼────────┼────────┼──────────┼──────────┤
│ Jisoo   │ 4건    │ 1건    │ 4.0/5   │ 2026-04-20│
└─────────┴────────┴────────┴──────────┴──────────┘
misc: 이미지 2건 / 영상 1건
품질 평가: 전체 평균 {x.x}/5
══════════════════════════════════
```

## 규칙
- 폴더가 비어 있으면 0건으로 표시
- 품질 로그가 없는 캐릭터는 평균점수 `-` 표시
- 미평가 = 폴더 파일 수 - log에 기록된 고유 파일 수
```

---

## skills/evaluate-all.md

```markdown
# Evaluate-All Skill
> 특정 캐릭터(또는 전체)의 프롬프트를 일괄 평가하고 log를 업데이트한다.

## 트리거
- `"evaluate-all {character}"` / `"jisoo 전체 평가해줘"` / `"전체 프롬프트 평가"`

## 실행 순서
1. 대상 캐릭터 결정 (미지정 시 전체 캐릭터 순차 실행)
2. `prompts/images/{character}/` 각 파일 읽기
3. `skills/prompt-evaluator.md` 기준으로 5개 항목 평가
4. 이미 log에 기록된 파일은 스킵 (`"전체 재평가"` / `"force"` 시 덮어쓰기)
5. 신규 평가 항목 log 추가 + `_summary.md` 업데이트
6. 결과 리포트 출력

## 출력 포맷
```
📊 일괄 평가 완료 — Jisoo (4건)
파일                          총점  캐릭터 씬/포즈 제품  조명  네거티브
jisoo-event-puma-01.json      5/5   ✅     ✅     ✅    ✅    ✅
평균: 4.0 / 5
```

## 규칙
- 평가 기준은 반드시 `skills/prompt-evaluator.md`를 따른다
- misc 폴더 프롬프트는 `prompt_quality_log/misc.md`에 기록
```

---

## skills/revise.md

```markdown
# Revise Skill
> 기존 프롬프트를 수정하고, 재평가 후 log에 기록한다. 수정·평가·기록을 한 번에 처리한다.

## 트리거
- `"revise {filename}"` / `"{filename} 수정해줘"` / `"{filename} 고쳐줘"`

## 실행 순서
1. 파일명으로 경로 탐색 (`prompts/images/{character}/` 우선, 없으면 `misc/`)
2. 파일 읽기 후 현재 `prompt` / `negative_prompt` 출력
3. 수정 요청 수신
4. 수정 적용 후 변경된 부분 하이라이트 출력
5. "이대로 저장할까요?" 확인
6. 확정 → 파일 저장
7. `skills/prompt-evaluator.md` 기준으로 재평가
8. `prompt_quality_log/{character}.md`에 수정 행 추가 (`(수정)` 표기)
9. `_summary.md` 업데이트

## 출력 포맷 (저장 후)
```
저장 완료: prompts/images/jisoo/jisoo-studio-hinok-01.json

📊 재평가 결과
총점  3 / 5  (이전: 2 / 5 → +1)
```

## 규칙
- 사용자 확인 전에는 파일을 저장하지 않는다
- 수정 전후 점수를 반드시 비교 표시
- Kling 프롬프트가 연결된 경우 수정 여부를 함께 묻는다
```

---

## skills/export.md

```markdown
# Export Skill
> 특정 캐릭터(또는 전체)의 프롬프트를 원하는 형식으로 정리해서 출력한다.

## 트리거
- `"export {character}"` / `"jisoo 프롬프트 내보내줘"` / `"전체 export"`

## 지원 포맷
| 포맷 | 설명 | 기본값 |
|---|---|---|
| `summary` | 파일명 + prompt 첫 줄 + 총점 | ✅ 기본 |
| `prompt-only` | prompt 텍스트만 나열 | |
| `full` | JSON 전문 | |
| `csv` | 파일명, 점수, 메모 CSV | |

## 실행 순서
1. 대상 캐릭터 및 포맷 결정
2. `prompts/images/{character}/` 파일 읽기
3. `prompt_quality_log/{character}.md` 점수 데이터 병합 (summary/csv)
4. 포맷에 맞게 출력

## 규칙
- 파일이 없으면 "해당 캐릭터의 프롬프트가 없습니다." 출력
- misc 포함 여부는 사용자에게 확인
- 출력 결과는 채팅에 표시. 별도 파일 저장은 사용자 요청 시에만 실행
```

---

## GUIDE.md

```markdown
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
```

---

세팅이 완료되면 "완료"라고 알려줘.
