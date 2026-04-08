# Lossless Prompt Compressor

[English](README.md) | [中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [한국어](README.ko.md) | [Español](README.es.md) | [Italiano](README.it.md) | [Português](README.pt-BR.md)

AI 지시 문서, 규칙 파일, 기획 문서, 제품 스펙을 압축합니다. 사실은 하나도 빠지지 않습니다.

요약이 아니라 압축입니다. 머신에 필요한 사실은 전부 유지하고, 포맷 오버헤드, 중복, 사람만 읽는 부분을 잘라냅니다. 선택적으로 AI 전용 초고밀도 속기로 재작성할 수도 있습니다.

## 모드

- **Strict Lossless** (기본): 기계적 정리(Pass 1) + 승인된 사실 보존 편집(Pass 2). 모든 사실이 살아남고, 사람도 읽을 수 있습니다.
- **AI-Lossless**: Pass 3 추가. 튜토리얼, 코칭, 일정 등 사람만 읽는 내용을 제거합니다.
- **AI-Only**: Pass 4 추가. 전보 스타일로 전면 재작성하여 밀도를 극대화합니다.

## 워크플로

| Pass | 유형 | 승인 | 일반적인 감소율 |
|------|------|------|----------------|
| 1 | 기계적 정리 | 자동 | 10-25% |
| 2 | 사실 보존 압축 | 항목별 | +5-15% |
| 3 | 사람용 콘텐츠 제거 | 항목별 | +15-25% |
| 4 | 전보 스타일 재작성 | 명시적 옵트인 | +25-40% |

Pass 1은 포장을 벗깁니다. Pass 2는 진공 포장합니다. Pass 3은 사람만 읽는 설명서를 빼냅니다. Pass 4는 전부 속기로 바꿉니다.

## 설계

- 기본값은 Strict Lossless: 요약이 아닌 압축
- 섹션 번호 안전: 교차 참조 앵커가 재번호 매기기되지 않음
- 승인 게이트: 기계적이지 않은 편집은 모두 사용자 승인 필요
- 23개 번호 기법, 전후 비교 예시 포함 → `references/techniques.md`
- 12개 엣지 케이스: 코드 중심 프롬프트부터 다국어 문서까지 대응

## 디렉터리 구조

```
lossless-prompt-compressor/
├── SKILL.md              — 운영 매뉴얼 (~350줄)
└── references/
    └── techniques.md     — 전체 기법 카탈로그 (예시 포함)
```

## 저자

[Wallny](https://github.com/wallmage)
