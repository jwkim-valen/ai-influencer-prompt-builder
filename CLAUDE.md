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
   → prompt_quality_log.md에 결과 기록

6. "수정할 부분이 있나요?" 질문

7. 수정 시 → 파일 업데이트 후 수정본 출력

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
   → characters/{id}.json 저장

이미지 없음
└─ 외형 텍스트로 설명 요청
   → master_prompt.md Character Card Template 형식으로 작성
   → 사용자 확인
   → 이름 입력받기
   → characters/{id}.json 저장
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
