# AI Influencer Prompt Builder
> Claude Code에서 바로 작동하는 AI 인플루언서 이미지/영상 프롬프트 자동 생성 시스템

---

## 시작하기

### 1단계 — 다운로드

터미널에서 아래 한 줄 입력:

```bash
git clone https://github.com/jwkim-valen/ai-influencer-prompt-builder
```

### 2단계 — Claude Code로 열기

```bash
cd ai-influencer-prompt-builder
claude
```

또는 Claude Code 앱에서 `Open Folder`로 폴더를 직접 선택.

### 3단계 — 바로 사용

Claude Code 채팅창에 원하는 장면을 설명하면 됩니다. 끝입니다.

---

## 사용 예시

**캐릭터 목록 확인**
```
캐릭터 목록 보여줘
```

**프롬프트 생성**
```
Jisoo 프롬프트 만들어줘
```
```
[이미지 첨부] Yuna 이 포즈로 프롬프트 생성해줘
```

**Kling 영상 프롬프트**
```
Kling 애니메이션 프롬프트 작성해줘
```

**프로젝트 현황 확인**
```
status
```

**프롬프트 수정**
```
revise jisoo-studio-hinok-01.json
```

---

## 등록된 캐릭터 (예시)

| 이름 | 특징 | 잘 맞는 씬 |
|---|---|---|
| Jisoo | 쿨톤, 한국인, 20대 초반 | 스튜디오, 미니멀 |
| Yuna | 웜톤, 한국인, 20대 초반 | 한강, 스튜디오 |
| Sofia | 올리브톤, 남유럽, 20대 중반 | 카페, 야외, 골든아워 |

> 예시 캐릭터를 참고해서 새 캐릭터를 등록하세요.
> `characters/` 폴더의 JSON 파일 구조를 따르면 됩니다.

---

## 폴더 구조

```
ai-influencer-prompt-builder/
├── CLAUDE.md                  ← Claude가 자동으로 읽는 워크플로우
├── GUIDE.md                   ← 전체 사용 가이드 (필독)
├── master_prompt.md           ← JSON 스키마, 공통 규칙
├── characters/                ← 등록된 캐릭터 카드
├── skills/                    ← 기능별 규칙 파일
├── prompts/
│   ├── images/                ← 생성된 이미지 프롬프트 JSON
│   └── video/                 ← 생성된 Kling 영상 프롬프트
└── prompt_quality_log/        ← 품질 평가 기록
```

---

## 자세한 사용법

`GUIDE.md` 파일을 참고하거나, Claude Code 채팅창에서:

```
GUIDE.md 열람
```

