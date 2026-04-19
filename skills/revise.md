# Revise Skill
> 기존 프롬프트를 수정하고, 재평가 후 log에 기록한다. 수정·평가·기록을 한 번에 처리한다.

---

## 트리거
사용자가 다음 중 하나를 입력하면 실행:
- `"revise {filename}"` / `"/revise {filename}"`
- `"{filename} 수정해줘"`
- `"{filename} 고쳐줘"`

---

## 실행 순서

1. 파일명으로 경로 탐색
   - `prompts/images/{character}/{filename}` 우선 탐색
   - 없으면 `prompts/images/misc/{filename}` 탐색
   - 못 찾으면: "파일을 찾을 수 없습니다. 정확한 파일명을 확인해주세요."

2. 파일 읽기 후 현재 `prompt` / `negative_prompt` 출력

3. 수정 요청 수신
   - 사용자가 수정 내용을 설명하거나 직접 수정 텍스트를 제공

4. 수정 적용 후 변경된 부분만 하이라이트하여 출력
   ```
   수정 전: ...holding product at chest height...
   수정 후: ...holding product vertically at ear height, label facing camera...
   ```

5. "이대로 저장할까요?" 확인

6. 확정 → 파일 저장

7. `skills/prompt-evaluator.md` 기준으로 재평가 실행

8. `prompt_quality_log/{character}.md`에 수정 행 추가
   - 파일명 끝에 `(수정)` 표기

9. `_summary.md` 업데이트

---

## 출력 포맷 (저장 후)

```
저장 완료: prompts/images/jisoo/jisoo-studio-hinok-01.json

📊 재평가 결과
─────────────────────────
캐릭터 충실도    ❌ 0 / 1
씬/포즈 명확도   ❌ 0 / 1
제품 디테일      ✅ 1 / 1
조명·무드 일관성  ✅ 1 / 1
네거티브 특이성   ✅ 1 / 1
─────────────────────────
총점             3 / 5  (이전: 2 / 5 → +1)
```

---

## 규칙

- 사용자 확인 전에는 파일을 저장하지 않는다
- 수정 전 점수와 수정 후 점수를 반드시 비교 표시
- Kling 프롬프트가 연결된 경우 수정 여부를 함께 묻는다
  ```
  연결된 Kling 프롬프트가 있습니다. 함께 수정할까요?
  prompts/video/jisoo/jisoo-studio-hinok-01-kling.txt
  ```
