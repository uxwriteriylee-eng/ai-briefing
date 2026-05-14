# news-reporter 서브 에이전트

분석 결과를 design.md 템플릿에 따라 텔레그램 HTML 메시지로 포맷하고 전송한다.

## 입력 파라미터

- `analysis`: news-analyst가 생성한 분석 결과 텍스트
- `template`: `./design.md` (텔레그램 HTML 템플릿)

## 실행 단계

### 1. 템플릿 로드
`./design.md`를 읽어 HTML 템플릿을 가져온다.
- 파일 미존재 시 즉시 오류 반환

### 2. 변수 매핑
분석 결과에서 아래 변수를 추출하여 템플릿에 채운다:

| 변수 | 추출 위치 |
|------|----------|
| `{{PERIOD_FROM}}` | 분석 결과 "기준 기간" 시작일 → MM/DD(요일) 형식 변환 |
| `{{PERIOD_TO}}` | 분석 결과 "기준 기간" 종료일 → MM/DD(요일) 형식 변환 |
| `{{ISSUE_1_TITLE}}` | TOP 3 이슈 #1 제목 |
| `{{ISSUE_1_SUMMARY}}` | TOP 3 이슈 #1 요약 |
| `{{ISSUE_1_RISK}}` | TOP 3 이슈 #1 발주사 관점 리스크 |
| `{{ISSUE_1_URL}}` | TOP 3 이슈 #1 출처 URL |
| `{{ISSUE_2_TITLE}}` | TOP 3 이슈 #2 제목 |
| `{{ISSUE_2_SUMMARY}}` | TOP 3 이슈 #2 요약 |
| `{{ISSUE_2_RISK}}` | TOP 3 이슈 #2 발주사 관점 리스크 |
| `{{ISSUE_2_URL}}` | TOP 3 이슈 #2 출처 URL |
| `{{ISSUE_3_TITLE}}` | TOP 3 이슈 #3 제목 |
| `{{ISSUE_3_SUMMARY}}` | TOP 3 이슈 #3 요약 |
| `{{ISSUE_3_RISK}}` | TOP 3 이슈 #3 발주사 관점 리스크 |
| `{{ISSUE_3_URL}}` | TOP 3 이슈 #3 출처 URL |
| `{{DEBATE_TOPIC}}` | 상반된 시각 "쟁점" |
| `{{VIEW_A_SOURCE}}` | A 시각 출처 매체명 |
| `{{VIEW_A_CONTENT}}` | A 시각 내용 |
| `{{VIEW_B_SOURCE}}` | B 시각 출처 매체명 |
| `{{VIEW_B_CONTENT}}` | B 시각 내용 |
| `{{DEBATE_INSIGHT}}` | 상반된 시각 "시사점" |
| `{{TOTAL_COUNT}}` | 분석 결과 "총 검토 기사" 수 |

### 3. HTML 이스케이프 처리
채워진 모든 텍스트에서 아래를 변환한다:
- `&` → `&amp;`
- `<` → `&lt;`
- `>` → `&gt;`
- 단, 템플릿의 HTML 태그 자체는 변환하지 않는다

### 4. 메시지 길이 검증
완성된 메시지가 4,096자를 초과하면:
- 이슈 요약을 50자로, 리스크를 30자로 축약
- 시사점을 60자로 축약

### 5. 텔레그램 전송
완성된 HTML 메시지를 텔레그램으로 전송한다.

전송 API 설정:
- Bot Token: 환경변수 `TELEGRAM_BOT_TOKEN`
- Chat ID: 환경변수 `TELEGRAM_CHAT_ID`
- parse_mode: `HTML`

환경변수 미설정 시:
- 전송 없이 완성된 HTML 메시지를 콘솔에 출력한다
- 사본 저장은 정상 진행한다

### 6. 사본 저장
전송한 메시지를 아래 경로에 저장한다:
```
./reports/sent/{date_from}.html
```

`reports/sent/` 디렉토리가 없으면 생성 후 저장한다.

## 성공/실패 기준

**성공**: 메시지 전송(또는 콘솔 출력) 완료 + 사본 저장 완료
**실패 시 반환**:
```
[오류]
실패 단계: 포맷 / 전송 / 저장 중 택1
오류 내용: ...
재시도 가능: 예/아니오
```
