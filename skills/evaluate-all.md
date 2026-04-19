# Evaluate-All Skill
> 특정 캐릭터(또는 전체)의 프롬프트를 일괄 평가하고 log를 업데이트한다.

---

## 트리거
사용자가 다음 중 하나를 입력하면 실행:
- `"evaluate-all {character}"` / `"/evaluate-all {character}"`
- `"jisoo 전체 평가해줘"`
- `"전체 프롬프트 평가"` (캐릭터 미지정 시 전체 캐릭터 순차 실행)

---

## 실행 순서

1. 대상 캐릭터 결정
   - 캐릭터 지정 있음 → `prompts/images/{character}/` 대상
   - 캐릭터 미지정 → `characters/` 목록의 모든 캐릭터 순차 처리

2. 각 `.json` 파일 읽기

3. `skills/prompt-evaluator.md` 기준으로 5개 항목 평가

4. 이미 `prompt_quality_log/{character}.md`에 기록된 파일은 **스킵**
   - 단, 사용자가 `"전체 재평가"` 또는 `"force"` 옵션을 명시하면 기존 행 덮어쓰기

5. 신규 평가 항목만 log에 추가

6. `_summary.md` 업데이트

7. 결과 리포트 출력

---

## 출력 포맷

```
📊 일괄 평가 완료 — Jisoo (4건)
─────────────────────────────────────────────────
파일                          총점  캐릭터 씬/포즈 제품  조명  네거티브
jisoo-event-puma-01.json      5/5   ✅     ✅     ✅    ✅    ✅
jisoo-studio-fashion-01.json  4/5   ❌     ✅     ✅    ✅    ✅
jisoo-studio-furriky-01.json  4/5   ✅     ❌     ✅    ✅    ✅
jisoo-studio-hinok-01.json    3/5   ❌     ❌     ✅    ✅    ✅
─────────────────────────────────────────────────
평균: 4.0 / 5   신규 평가: 0건 스킵 (이미 평가됨)
```

---

## 규칙

- 평가 기준은 반드시 `skills/prompt-evaluator.md`를 따른다
- misc 폴더 프롬프트는 `prompt_quality_log/misc.md`에 기록
- 한 번에 너무 많으면 캐릭터별로 나눠서 처리 후 합산
