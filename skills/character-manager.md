# Character Manager Skill
> 캐릭터 등록 · 로드 · 선택 규칙

---

## 캐릭터 파일 구조

모든 캐릭터는 `characters/` 폴더에 개별 JSON으로 저장된다.

```
characters/
├── sofia.json
├── yuna.json
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
분석 완료 후 `card` 초안 생성 → 사용자 확인 → `characters/{id}.json` 저장.

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
