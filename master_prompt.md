# Master Prompt — NB2 JSON Schema & Shared Rules

## JSON 스키마 (Dense Narrative Format)
> 모든 이미지 프롬프트는 반드시 이 구조를 따른다.

```json
{
  "prompt": "Dense ultra-specific narrative. 반드시 포함: subject description + action + product/object details + lighting commands (프롬프트 내부에 직접 삽입). Direct Commands 3개 이상 포함.",
  "negative_prompt": "Mandatory Negative Stack으로 시작 + 씬별 추가 항목",
  "settings": {
    "resolution": "2K | 4K",
    "aspect_ratio": "9:16 | 1:1 | 16:9",
    "style": "hyper-realistic, documentary, UGC, candid amateur",
    "lighting": "씬에 맞는 조명 서술",
    "camera": {
      "lens": "35mm equivalent, front-facing phone camera",
      "angle": "eye-level | slight high angle | POV",
      "framing": "close-up selfie | medium shot | full body",
      "depth_of_field": "phone-natural – background soft but recognisable, NOT blurred",
      "focus": "face sharp, product label sharp and legible, background present and identifiable"
    },
    "image_quality_simulation": {
      "sharpness": "tack_sharp | slightly_soft_edges",
      "noise": "clean_digital | minor unfiltered sensor grain",
      "compression_artifacts": true,
      "dynamic_range": "limited | hdr_capable",
      "white_balance": "slightly_warm | cool_fluorescent | natural_daylight",
      "lens_imperfections": ["minor lens distortion", "subtle vignetting"]
    },
    "explicit_restrictions": {
      "no_professional_retouching": true,
      "no_studio_lighting": true,
      "no_ai_beauty_filters": true,
      "no_high_end_camera_look": true
    },
    "color_grading": "warm | neutral | natural | cinematic"
  }
}
```

---

## Mandatory Negative Stack
> negative_prompt는 반드시 이것으로 시작한다:

```
bokeh, blurred background, shallow depth of field, background out of focus,
studio lighting, professional photography, retouched skin, beauty filter,
smooth skin, poreless, airbrushed, plastic skin, editorial lighting,
DSLR look, high-end camera, perfectly symmetrical face, unrealistic proportions,
CGI, render, illustration, painting, digital art, anime, cartoon,
oversaturated, HDR glow, lens flare, vignette, watermark, text overlay,
extra limbs, deformed hands, wrong fingers, mutated, blurry product label,
illegible text, distorted packaging, wrong product shape, missing label details, generic bottle
```

---

## Direct Commands Rule
> positive prompt 안에 반드시 3개 이상 직접 삽입:

- "Do not smooth skin."
- "Do not blur background."
- "Label must be fully legible."
- "No beauty filter."
- "Preserve exact facial structure."
- "Do not reshape body proportions."
- "No colour grading beyond natural."

---

## Imperfections Rule (자연스러움 강제)

### Skin
```
visible pores, mild redness, peach fuzz on cheeks, light acne marks, under-eye shadow, crow's feet, laugh lines
```

### Hair
```
flyaway strands, not salon-perfect, slight frizz, natural root growth, windswept edges
```

### Body
```
natural proportions, no editorial reshaping, real posture, slight asymmetry
```

---

## Character Card Template
> 한 번 정의하면 이후 프롬프트에 그대로 복사. 절대 paraphrase 금지.

```
"[gender], [age], [ethnicity] skin tone, [face shape] with natural asymmetry,
 [eye colour] eyes with [lash/eye details], [skin description with specific imperfections],
 [hair colour and style – proportions]"
```

### 예시 — Sofia
```
"woman, mid 20s, Southern European olive skin tone, slightly oval face with natural asymmetry,
 dark brown eyes with slight under-eye shadow, natural lashes, olive complexion with visible pores,
 peach fuzz on cheeks, dark brown naturally wavy slightly frizzy hair,
 average build with natural proportions"
```

---

## The Noise Trap Warning
ISO 3200 이상 또는 heavy film grain → 네온/야간/어두운 씬에서 디지털 아트 편향 발동.
→ 카메라 노이즈보다 물리 기반 조명으로 리얼리즘 구현할 것.

## Structural Preservation Rule
> "Preserve exact facial structure, proportions, and identity. Do not average or normalise features."

## Product Detail Rule (레퍼런스 이미지 있을 때)
1. 업로드된 이미지 완전히 분석
2. 형태 · 크기 · 라벨 텍스트 · 색상 · 아이콘 · 소재 · 마감 · 캡 스타일 추출
3. 모든 디테일을 prompt에 작성
4. negative_prompt에 추가: "blurry product label, illegible text, distorted packaging, wrong product shape, missing label details, generic bottle"
5. 레퍼런스 없으면 사용자가 설명한 것만 사용. 브랜드 디테일 임의 생성 금지.

## Phone Selfie #1 Rule
**iPhone 전면 카메라 셀피에는 배경 블러 없음.**
```
NEVER: bokeh, blurred background, shallow depth of field, background out of focus
ALWAYS: "background nearly in focus, real environment, soft but fully identifiable"
        → 배경의 구체적 오브젝트를 이름으로 명시
```
