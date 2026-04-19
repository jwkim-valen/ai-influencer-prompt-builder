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
