# Export Skill
> 특정 캐릭터(또는 전체)의 프롬프트를 원하는 형식으로 정리해서 출력한다.

---

## 트리거
사용자가 다음 중 하나를 입력하면 실행:
- `"export {character}"` / `"/export {character}"`
- `"jisoo 프롬프트 내보내줘"`
- `"전체 export"` (전체 캐릭터)

---

## 지원 포맷

| 포맷 | 설명 | 기본값 |
|---|---|---|
| `summary` | 파일명 + prompt 첫 줄 + 총점 목록 | ✅ 기본 |
| `prompt-only` | prompt 텍스트만 추출하여 순서대로 나열 | |
| `full` | JSON 전문을 캐릭터별로 묶어서 출력 | |
| `csv` | 파일명, 점수, 메모를 CSV 형태로 출력 | |

포맷 미지정 시 `summary` 사용.
지정 예시: `"jisoo export prompt-only"` / `"전체 csv로 내보내줘"`

---

## 실행 순서

1. 대상 캐릭터 및 포맷 결정

2. `prompts/images/{character}/` 파일 목록 읽기

3. 각 파일 읽기 및 포맷에 따라 데이터 추출

4. `prompt_quality_log/{character}.md`에서 점수 데이터 병합 (summary / csv 포맷)

5. 포맷에 맞게 출력

---

## 출력 포맷 예시

### summary (기본)
```
📦 Jisoo 프롬프트 목록 (4건)
─────────────────────────────────────────────────
01. jisoo-event-puma-01.json          [5/5]
    FULL BODY SHOT FROM HIGH OVERHEAD ANGLE. Subject fully visible...

02. jisoo-studio-fashion-01.json      [4/5]
    3/4 BODY SHOT. Subject visible from head to above knees...

03. jisoo-studio-furriky-01.json      [4/5]
    UPPER BODY PORTRAIT. Subject visible from head to chest...

04. jisoo-studio-hinok-01.json        [3/5]
    UPPER BODY PORTRAIT. Face occupies left 60% of frame...
─────────────────────────────────────────────────
총 4건 | 평균 4.0 / 5
```

### csv
```
파일명,총점,캐릭터충실도,씬/포즈,제품디테일,조명일관성,네거티브특이성,메모
jisoo-event-puma-01.json,5,✅,✅,✅,✅,✅,-
jisoo-studio-fashion-01.json,4,❌,✅,✅,✅,✅,캐릭터 충실도 낮음
...
```

---

## 규칙

- 파일이 없으면 "해당 캐릭터의 프롬프트가 없습니다." 출력
- misc 포함 여부는 사용자에게 확인 (`"misc 포함할까요?"`)
- 출력 결과는 채팅에 표시. 별도 파일 저장은 사용자 요청 시에만 실행
