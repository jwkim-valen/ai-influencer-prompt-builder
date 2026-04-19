# Antigravity — AI Influencer JSON Prompt Generator
> Claude Code에서 바로 작동하는 JSON 프롬프트 자동 생성 시스템

---

## 📁 폴더 구조

```
antigravity/
├── CLAUDE.md               ← Claude Code가 자동으로 읽는 핵심 설정
├── master_prompt.md        ← JSON 스키마 + 공통 규칙 (Negative Stack, Imperfections 등)
├── README.md               ← 이 파일
├── skills/
│   └── ai-influencer.md   ← 이미지(Higgsfield) + 영상(Kling) 스킬 규칙
└── prompts/
    ├── images/             ← 생성된 이미지 JSON 프롬프트
    │   └── sofia-cafe-prime-01.json  (예시)
    └── video/              ← 생성된 Kling 영상 프롬프트
```

---

## 🚀 셋업 방법 (3단계)

### 1단계 — 폴더를 Claude Code 프로젝트로 열기
```bash
# 이 antigravity 폴더를 Claude Code로 열기
# 터미널에서:
cd antigravity
claude
```
또는 Claude Code 앱에서 "Open Folder"로 `antigravity` 폴더를 직접 선택.

### 2단계 — Claude Code가 CLAUDE.md를 자동 인식
Claude Code는 프로젝트 루트의 `CLAUDE.md`를 자동으로 읽는다.
별도 설정 없이 바로 워크플로우가 활성화된다.

### 3단계 — 바로 사용 시작
Claude Code 채팅창에 원하는 장면을 한국어로 설명하면 된다.

---

## 💬 사용 예시

### 예시 1 — 간단한 설명
```
카페에서 프라임 음료수 들고 셀피 찍는 20대 여성
```
→ Claude가 모르는 정보(헤어, 피부톤 등)만 추가로 물어보고 JSON 자동 생성

### 예시 2 — 상세한 설명
```
올리브 피부의 20대 중반 여성, 곱슬기 있는 갈색 머리,
헬스장에서 운동 후 물병 들고 있는 캔디드 샷,
흰 탱크탑, 자연광
```
→ 추가 질문 없이 바로 JSON 생성

### 예시 3 — 제품 레퍼런스 이미지와 함께
```
이 제품 들고 공원에서 찍은 셀피 만들어줘 [이미지 첨부]
```
→ Claude가 이미지 분석 후 라벨/색상/형태 자동 추출

---

## ⚙️ 커스터마이징

### 새 캐릭터 추가
`master_prompt.md` 하단에 Character Card 추가:
```
### 캐릭터명
"[gender], [age], [ethnicity] skin tone, ..."
```

### 새 스킬 추가
`skills/` 폴더에 새 `.md` 파일 생성.
`CLAUDE.md`의 워크플로우 섹션에 해당 스킬 참조 추가.

### 플랫폼 기본값 변경
`master_prompt.md`의 Platform Defaults 또는
`skills/ai-influencer.md`의 Camera Defaults 수정.

---

## 📌 핵심 규칙 요약

| 규칙 | 내용 |
|---|---|
| 셀피 배경 블러 금지 | iPhone 전면카메라엔 bokeh 없음 |
| Direct Commands | prompt 안에 최소 3개 삽입 |
| 제품 디테일 | 레퍼런스 없으면 임의 생성 금지 |
| 노이즈 주의 | ISO 3200↑ + heavy grain → 디지털 아트 편향 |
| Character Card | paraphrase 금지, 그대로 복사 |

---

## 🔄 생성 결과물 위치

- 이미지 JSON: `prompts/images/{name}-{location}-{product}-{nn}.json`
- Kling 영상: `prompts/video/{name}-{location}-{product}-{nn}-kling.txt`
