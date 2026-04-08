# Lossless Prompt Compressor

[![GitHub stars](https://img.shields.io/github/stars/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/network/members)
[![GitHub watchers](https://img.shields.io/github/watchers/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/watchers)
[![GitHub last commit](https://img.shields.io/github/last-commit/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/commits/main)
[![GitHub repo size](https://img.shields.io/github/repo-size/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Top language](https://img.shields.io/github/languages/top/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Compression](https://img.shields.io/badge/compression-lossless-111927?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Focus](https://img.shields.io/badge/focus-prompt%20compaction-0f766e?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Works with](https://img.shields.io/badge/works%20with-LLM%20%7C%20agent%20%7C%20chat-4f46e5?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Output](https://img.shields.io/badge/output-smaller%20context-b45309?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Guarantee](https://img.shields.io/badge/guarantee-no%20facts%20lost-2563eb?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)

[English](README.md) | [中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | 한국어 | [Español](README.es.md) | [Italiano](README.it.md) | [Português](README.pt-BR.md)

## 왜 만들었는가

시스템 프롬프트의 모든 토큰은 매 API 호출마다 내는 세금이다. 35,000 단어짜리 지시 파일은 약 48,000 토큰의 컨텍스트를 의미한다. 모델이 사용자 메시지를 읽기도 전에 윈도우 대부분이 이미 채워져 있다. 하루 수천 건의 요청을 곱하면, 포맷팅과 중복과 사람만 읽을 문장에 돈을 태우고 있는 셈이다.

이 문제를 해결하려고 만들었다. Lossless Prompt Compressor는 AI 지시 문서 -- 시스템 프롬프트, CLAUDE.md 파일, 에이전트 규칙, 제품 스펙, 마스터 플랜 -- 를 받아서 강하게 압축한다. 요약이 아니다. 압축이다. 모든 사실, 모든 필드명, 모든 제약 조건, 모든 교차 참조가 그대로 남는다. 잘려나가는 것은 포장이다: 모델이 필요 없는 Markdown 포맷, 중복된 서술, 튜토리얼 스캐폴딩, 동기부여 필러, 그리고 토큰만 소비하고 정보는 전달하지 않는 문법적 접속사.

일반적인 제품 스펙에서 4개 패스를 모두 돌리면 50-65% 감소를 볼 수 있다. 매 API 호출에서 실제로 절약되는 비용이고, 정말 중요한 대화를 위해 확보되는 컨텍스트 윈도우다.

이 압축기는 [vibecheck](https://github.com/wallmage/vibecheck)의 압축 패스에서도 핵심 엔진으로 사용된다.

## "무손실"이 진짜 의미하는 것

이것은 요약이 아니다. 요약은 무엇이 중요한지 판단하고 나머지를 버린다. 지시 파일에 그렇게 하면 위험하다 -- 제약 조건 하나가 빠지면 모델이 명시적으로 배제한 기능을 환각으로 생성할 수 있다.

무손실이란: 원본 문서에 "프리티어 보조금을 월 매출의 3%로 제한"이라고 적혀 있으면, 압축된 버전도 정확히 같은 말을 한다는 뜻이다. 사실이 삭제되거나, 모호하게 바뀌거나, 조용히 병합되는 일은 없다. 섹션 번호는 고정되어 "S8.7.3 참조" 같은 교차 참조가 절대 깨지지 않는다. AI가 압축된 버전에서 생성하는 코드와 아키텍처 결정은 원본에서 생성하는 것과 동일하다.

이 보장은 승인 게이트로 강제된다 -- 기계적이지 않은 모든 편집이 전후 diff와 함께 제안되고, 승인한 경우에만 적용된다.

## 4개 패스에 걸친 23가지 기법

압축기는 4개의 점진적 패스로 작동한다. 각 패스는 더 깊이 들어간다. 어디서 멈출지는 당신이 결정한다.

### Pass 1 -- 기계적 정리 (자동, 10-25% 감소)

판단이 필요 없다. 이 패스는 LLM이 있든 없든 동일하게 파싱하는 포맷을 제거한다:

- **Markdown 제거**: `##` 헤더, `**볼드**`, `- ` 불릿, `| 테이블 |` 파이프, ` ``` ` 코드 펜스, `> ` 인용, `---` 구분선 -- 모두 제거. 텍스트와 구조는 남기고, 장식만 제거.
- **포맷 변환**: 테이블은 세미콜론 구분 행으로, 불릿 리스트는 인라인 세미콜론 구분 항목으로, 링크 문법 `[텍스트](url)`은 URL로 축약.
- **검증 스캔**: 제거 후 전체 스캔으로 Markdown 잔여물 포착. 16진수 컬러(`#FF0000`), 정규식 글로브(`*.txt`), 셸 파이프, YAML 프론트매터는 콘텐츠로 인식하여 보존.

기법 카탈로그의 기법 1-2에 해당. 어떤 문서에도 안전하게 실행 가능. 일반적인 절약: 10-25% 단어 감소.

### Pass 2 -- 사실 보존 압축 (승인 필요, +5-15%)

여기서부터 창의적이 된다. 제안된 각 편집이 예상 절약량과 함께 보여지고, 승인 후에만 적용:

- **교차 참조 중복 제거** (기법 3): 3곳에서 같은 사실이 서술된 경우, 1곳을 정규 표현으로 남기고 나머지를 "S6에 따름" 같은 포인터로 교체.
- **코드 블록 압축** (기법 4): struct/class 정의를 모든 필드명, 타입, enum 값을 보존하는 컴팩트한 인라인 설명으로 변환. 예외: Python/YAML/Makefile은 공백이 의미를 가지므로 원본 유지.
- **JSON 설정 압축** (기법 5): 전체 JSON 예제를 스키마 설명으로 변환.
- **버전별 상세 삭감** (기법 6): 요약 테이블과 버전별 상세가 같은 내용이면 상세 삭제.
- **동기부여 블록 압축** (기법 7): "왜 이것이 중요한가" 섹션을 핵심 기술적 통찰로 압축.
- **설계 근거 압축** (기법 8): 4개 단락의 논증을 주요 이유가 포함된 1문장으로.
- **유스케이스 목록 압축** (기법 9): 여러 단락에 걸친 장황한 유스케이스를 각 1줄로.
- **ASCII 목업 압축** (기법 10): 토큰을 많이 소비하는 ASCII 아트를 컴팩트한 텍스트 설명으로.

모든 편집이 엄격한 검증을 통과해야 한다: 모든 사실 존재, AI가 동일한 결정을 내림, 모호함 없음, 각 사실이 정규 섹션까지 추적 가능.

### Pass 3 -- 사람용 콘텐츠 제거 (승인 필요, +15-25%)

각 후보에 대한 판단 기준은 하나다: "이걸 제거하면 AI의 출력 품질이 떨어지는가?" 아니라면, 대상이다.

- **초보자 튜토리얼** (기법 11): "앱 스토어에서 IDE를 설치하세요. 30-60분 걸립니다..." 가 무엇을 설치해야 하는지의 목록이 됨. 방법은 제외.
- **연습 문제와 학습 활동** (기법 12): AI에게 남의 연습 문제는 불필요.
- **일별 스케줄** (기법 13): 기상 시간과 커피 브레이크 삭제. 기능명과 기술 요구사항은 유지.
- **코칭과 동기부여 콘텐츠** (기법 14): "당황하지 마세요"와 "축하합니다!"는 모델에 정보량 제로.
- **검증 매트릭스** (기법 15): 모든 기능이 "출시"라고 쓰인 30행 테이블이 2줄이 됨.
- **경쟁사 비교표** (기법 16): "모든 차원에서 우리가 이긴다"는 사람을 동기부여하지, 모델에는 무관.
- **사람용 워크플로 예시** (기법 17): "당신의 하루는 이렇습니다" 류의 워크스루.

Pass 3은 '무엇을'과 '왜'를 보존하고, '사람이 어떻게 하는지'를 제거한다.

### Pass 4 -- 전보 스타일 재작성 (명시적 옵트인, +25-40%)

문서 전체를 초고밀도 속기체로 재작성한다. 모든 단어가 정보를 담아야 한다. 사람이 읽기 편하게 하려고만 존재하는 단어는 삭제. AI가 유일한 소비자인 문서용.

- **연결 구문 제거** (기법 18): "이것은 ~를 의미한다", "~를 가능하게 한다", "~하기 위해", "주목할 점은" -- 모두 삭제.
- **세미콜론 구분 프래그먼트** (기법 19): 완전한 문장이 키-값 프래그먼트가 됨. 관사, "이다/있다", 필러 주어 삭제.
- **다중 문장 축약** (기법 20): 하나의 개념을 설명하는 5개 문장이 핵심 사실로.
- **암시적 주어 생략** (기법 21): 섹션 제목이 "검색 기능"이면 매 문장 시작에 "검색 기능은"을 쓸 필요 없음.
- **컴팩트 표기법** (기법 22): 옵션에 슬래시(`immediate/batched/silent`), 흐름에 화살표(`offline -> local queue -> sync`), 관계에 콜론(`Auth: JWT + refresh`).
- **반복 구조 패턴 압축** (기법 23): 패턴을 한 번 기술하고, 이후는 속기.

현대 LLM은 전보 스타일 출력을 완벽한 충실도로 파싱한다. 세미콜론, 화살표, 슬래시 구분 옵션 -- 어떤 모델 계열도 문제없다.

## 모드

세 가지 모드, 각각 이전 것 위에 쌓인다:

- **Strict Lossless** (기본): Pass 1 + Pass 2. 모든 사실이 살아남고, 문서는 여전히 사람이 읽을 수 있다. 당신과 AI 모두 참조하는 파일에 적합 -- CLAUDE.md, 공유 에이전트 규칙, 유지 중인 스펙. 안전하고, 보수적이고, 실질적 비용 절감.

- **AI-Lossless**: Pass 3 추가. 튜토리얼, 코칭, 일별 스케줄, 검증 매트릭스 등 사람 독자만 돕는 콘텐츠를 제거. AI가 받는 신호는 완전히 동일. AI가 소비하지만 당신이 거의 다시 읽지 않는 시스템 프롬프트와 에이전트 지시에 적합.

- **AI-Only**: Pass 4 추가. 전보 스타일로 전면 재작성. 최대 밀도, 읽기 편하지 않음. 절약된 토큰 하나하나가 하루 수천 건의 호출에 곱해지는 대량 프로덕션 프롬프트에 적합.

## 워크플로

| Pass | 하는 일 | 승인 | 일반적인 감소율 |
|------|--------|------|----------------|
| 1 | Markdown 포맷 제거, 테이블/리스트 변환 | 자동 | 10-25% |
| 2 | 사실 중복 제거, 코드/JSON/논증 압축 | 항목별 | +5-15% |
| 3 | 튜토리얼, 코칭, 스케줄, 사람용 스캐폴딩 제거 | 항목별 | +15-25% |
| 4 | 전보 스타일 전면 재작성, 문장 대신 프래그먼트 | 명시적 옵트인 | +25-40% |

일반적인 제품 스펙에서 4개 패스 전체: **50-65% 감소**. 35,000 단어 문서에서 API 호출당 약 25,000-32,000 토큰 절약.

## 설계 원칙

- **기본은 무손실**: Strict Lossless가 기본 모드. 더 공격적인 압축은 명시적 옵트인 필요.
- **섹션 번호는 신성불가침**: 교차 참조 앵커는 절대 재번호 매기기되지 않음. 주변 섹션이 삭제되어도. "S8.7.3 참조"는 항상 유효.
- **승인 게이트**: 기계적이지 않은 모든 편집이 diff와 예상 절약량과 함께 제안됨. 승인 없이는 아무것도 바뀌지 않음.
- **23가지 기법 + 예시**: 완전한 전후 비교 카탈로그가 `references/techniques.md`에 있음.
- **12가지 엣지 케이스 처리**: 짧은 문서, 코드 중심 프롬프트, 이미 압축된 입력, 비영어/CJK 텍스트, 임베디드 URL, 다국어 문서, Markdown에 관한 문서, 문학/법률 문서, 초대형 문서 등.

## 디렉터리 구조

```
lossless-prompt-compressor/
├── SKILL.md              — 운영 매뉴얼 (~350줄)
└── references/
    └── techniques.md     — 전체 기법 카탈로그 (전후 비교 예시 포함)
```

## 저자

[Wallny](https://github.com/wallmage)
