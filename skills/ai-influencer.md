# AI Influencer Skill — NanoBanana 2
> Part 1: Higgsfield 이미지 프롬프트 / Part 2: Kling 영상 프롬프트

---

## PART 1 — IMAGE (Higgsfield / Nano Banana Pro)

**목표:** 실제 사람이 실제 폰으로 찍은 것처럼 보이는 JSON 프롬프트 생성.

### Camera Defaults (셀피 기준)
```
Lens:           35mm equivalent, front-facing phone camera
Aperture:       f/2.2
ISO:            400
Style:          phone-selfie, documentary realism, candid amateur
Depth of field: phone-natural – background soft but recognisable, NOT blurred
Focus:          face sharp, product label sharp and legible, background present and identifiable
```

### Camera Defaults (후면 캔디드 기준)
```
Lens:           28mm equivalent, rear phone camera
Aperture:       f/1.8
ISO:            200–800 (ambient에 따라)
Style:          candid street photography, UGC, documentary
Depth of field: subject sharp, background identifiable
```

### Lighting 레퍼런스
| 씬 | 조명 서술 |
|---|---|
| 골든아워 야외 | warm directional sunlight from left, golden hour glow, long soft shadows |
| 실내 낮 | soft diffused natural window light from side, neutral warm tones |
| 카페 실내 | warm ambient tungsten overhead, soft fill from window, slight shadow under jaw |
| 흐린 야외 | overcast soft flat lighting, no harsh shadows, even skin tones |
| 밤 야외 | mixed city ambient, neon bounce, underexposed background, slight noise |

### Platform Defaults
```
Resolution:   2K (수정 중) → 4K (최종)
Aspect ratio: 9:16 (기본 세로) | 1:1 (정방형) | 16:9 (가로)
Style:        hyper-realistic, candid, UGC
```

---

## PART 1 — 출력 포맷

사용자 입력을 받아 아래 포맷으로 완성된 JSON을 빌드한다.

```json
{
  "prompt": "[character_card]. [action + pose]. [location description with named background objects]. [lighting description]. [product description if applicable, with full label detail]. [direct commands: minimum 3]. Preserve exact facial structure, proportions, and identity. Do not average or normalise features.",
  "negative_prompt": "[Mandatory Negative Stack from master_prompt.md] + [scene-specific additions]",
  "settings": {
    "resolution": "2K",
    "aspect_ratio": "9:16",
    "style": "hyper-realistic, phone-selfie, documentary realism, candid amateur, UGC",
    "lighting": "[scene lighting]",
    "camera": {
      "lens": "35mm equivalent, front-facing phone camera",
      "angle": "eye-level, slight high angle",
      "framing": "close-up selfie, head and shoulders, product visible in frame",
      "depth_of_field": "phone-natural – background soft but recognisable, NOT blurred",
      "focus": "face sharp, product label sharp and legible, background present and identifiable"
    },
    "image_quality_simulation": {
      "sharpness": "tack_sharp",
      "noise": "minor unfiltered sensor grain",
      "compression_artifacts": true,
      "dynamic_range": "limited",
      "white_balance": "slightly_warm",
      "lens_imperfections": ["minor lens distortion", "subtle vignetting"]
    },
    "explicit_restrictions": {
      "no_professional_retouching": true,
      "no_studio_lighting": true,
      "no_ai_beauty_filters": true,
      "no_high_end_camera_look": true
    },
    "color_grading": "natural"
  }
}
```

---

## PART 2 — VIDEO (Kling 3.0)

이미지 확정 후, 해당 이미지를 기반으로 Kling 텍스트 프롬프트를 작성한다.

### Kling 프롬프트 구조
```
[motion description – 무엇이 어떻게 움직이는지, 카메라 이동 포함]
[character action – 자연스럽고 미세한 동작]
[environment motion – 배경의 움직임]
[duration hint – 3–5 seconds]
```

### 모션 규칙
- **미세한 동작 우선**: 크게 움직이면 인공적으로 보임
  - ✅ slight hair movement, natural breathing, small product shift, micro smile
  - ❌ dramatic head turn, walking, jumping
- **카메라**: 기본적으로 static. 원하면 slow push-in 또는 gentle handheld shake
- **배경**: subtle environmental movement (leaves rustling, people passing blurred, steam rising)

### Kling 출력 예시
```
Subject holds product naturally at chest height, slight breathing motion visible,
hair moves gently from soft breeze. Camera remains static with very subtle handheld micro-shake.
Background coffee shop patrons move softly out of focus. Natural warm light flickers slightly.
Duration: 4 seconds. Loop-friendly ending.
```

---

## 저장 경로 규칙
```
등록된 캐릭터:
  이미지: prompts/images/{character}/{character}-{location}-{product}-{number:02d}.json
  영상:   prompts/video/{character}/{character}-{location}-{product}-{number:02d}-kling.txt

미등록 캐릭터 (인라인 빌드):
  이미지: prompts/images/misc/new-{location}-{product}-{number:02d}.json
  영상:   prompts/video/misc/new-{location}-{product}-{number:02d}-kling.txt
```
예시:
```
prompts/images/sofia/sofia-cafe-prime-01.json
prompts/video/sofia/sofia-cafe-prime-01-kling.txt

prompts/images/misc/new-cafe-prime-01.json
prompts/video/misc/new-cafe-prime-01-kling.txt
```
