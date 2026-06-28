# 클라우드 루틴 셋업 가이드

이 저장소를 Claude Code 클라우드 루틴에 연결하면, 노트북을 꺼놔도
Anthropic 서버에서 매일 아침·저녁 자동으로 pain point 리포트를 생성·커밋합니다.

## 구조
```
.
├── SKILL.md              ← 루틴이 매번 읽는 작업 지시서 (이게 핵심)
├── data/
│   └── feed_specs.csv    ← 한국 언론사 RSS 주소록 약 155개 (수집 소스)
├── reports/              ← 생성된 리포트가 쌓이는 곳
└── README.md             ← 이 파일
```
> API 키도, agent.py도, 파이썬 의존성도 필요 없습니다.
> 루틴 세션 자체가 Claude라서, 그 안에서 직접 RSS 수집·분석·파일작성·커밋을 합니다.

## 0단계 — RSS 주소록 넣기 (한 번만)
이 루틴은 `data/feed_specs.csv`에 적힌 언론사 RSS들을 매 실행 때 읽어 기사를 모읍니다.
그 주소록을 knews-rss에서 가져와 우리 저장소에 직접 넣어두면, 남의 서비스에 의존하지 않습니다.
1. https://github.com/akngs/knews-rss/blob/main/data/feed_specs.csv 에서 **Download raw file**
   (또는 Raw → 전체 복사)로 `feed_specs.csv`를 받는다.
2. 이 저장소의 `data/feed_specs.csv` 경로에 넣고 커밋한다.
> 이 파일에는 경향신문 `https://www.khan.co.kr/rss/rssdata/total_news.xml` 같은
> 각 언론사의 **원본 RSS 주소**가 들어 있습니다. 가끔(수개월에 한 번) 최신본으로 갱신하면 됩니다.

## 1단계 — 저장소 준비
1. 이 폴더를 GitHub 저장소로 push (루틴은 **GitHub 저장소 클로닝**이 필요).
2. Claude.ai 계정에 그 GitHub 저장소를 연결.

## 2단계 — 루틴 생성
`claude.ai/code/routines` 접속 → **New routine** → **Remote** 선택.
- **Repository:** 위에서 만든 저장소
- **Prompt(지시문):** 아래를 그대로 붙여넣기
  ```
  저장소 루트의 SKILL.md를 읽고, 거기 적힌 작업 순서대로
  오늘자 한국 B2C pain point 리포트를 생성해 reports/ 폴더에 저장하고 커밋해줘.
  ```
- **Schedule(스케줄):** 아침·저녁 두 개의 트리거 추가
  - 매일 07:00 (Asia/Seoul)
  - 매일 19:00 (Asia/Seoul)
  - (시간은 본인 타임존으로 입력하면 자동 변환됩니다. 1시간보다 잦은 주기는 불가.)

> 대화형으로 만들 수도 있습니다. Claude Code 세션에서
> "이 저장소로 매일 아침 7시·저녁 7시에 SKILL.md 작업을 실행하는 루틴 만들어줘"
> 라고 말하면 알아서 설정해 줍니다.

## 3단계 — 결과 받기
- 매 실행 후 `reports/pain-point-날짜-시간대.md` 가 저장소에 커밋됩니다.
- 알림으로 받고 싶으면 루틴 프롬프트 끝에 한 줄 추가:
  "완료되면 Slack #아이디어 채널에 요약 3줄을 보내줘" (Slack 커넥터 연결 시)

## 실행 한도 (요금제별, 하루 기준)
- Pro: 5회 / Max: 15회 / Team·Enterprise: 25회
- 아침·저녁 2회면 Pro 한도(5회) 안에서 충분합니다.

## 트리거 종류 (참고)
- **Schedule:** 시간 기반 (이 용도에 사용)
- **API:** 전용 HTTP 엔드포인트로 수동 트리거
- **GitHub:** 저장소 이벤트(예: push)로 트리거

## 주의
- 위 사양(한도·트리거·경로)은 외부 자료 기준이라, 실제 화면 문구·옵션은
  claude.ai에서 다를 수 있습니다. 루틴은 research preview 단계라 바뀔 수 있어요.
- 루틴이 커밋·메시지로 하는 모든 행동은 **본인 계정 명의**로 기록됩니다.
- 첫 2~3회는 출력 품질을 직접 확인하고, SKILL.md의 점수 기준·키워드를 다듬으세요.
