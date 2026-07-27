# LLM Wiki Template

로컬 논문 PDF를 근거로 축적하는 연구용 LLM Wiki 템플릿입니다.

## AI로 Wiki 만들기

Claude Code 또는 Codex를 빈 폴더에서 실행한 뒤, 아래와 같이 요청하세요.

```text
다음 GitHub 저장소의 CLAUDE.template.md를 끝까지 읽고,
그 안의 Initial Wiki Setup 지침에 따라 로컬 LLM Wiki를 구축해줘.
파일을 만들기 전에 먼저 내 연구 분야와 사용할 5–10개 카테고리를 질문해줘.

https://github.com/hejeong1219/llm-wiki-template
```

AI는 먼저 연구 분야와 카테고리를 질문해야 하며, 답변 전에는 분야·카테고리·논문·과학적 내용을 임의로 생성해서는 안 됩니다.

## 포함 파일

- `CLAUDE.template.md` — LLM Wiki 구조, 근거 규칙, 논문 수집 절차 및 초기 설정 지침

Claude Code에서는 완성된 규칙을 `CLAUDE.md`로, Codex에서는 `AGENTS.md`로 저장합니다.

## 기본 원칙

- 로컬에 보관된 논문만 과학적 근거로 사용
- Wiki가 부족하면 원본 PDF를 다시 확인
- 관련 논문이 없으면 추측하지 않고 PDF를 요청
- `papers/ → sources/ → wiki/`의 3단계 구조 유지

