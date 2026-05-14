# 텔레그램 메시지 디자인 템플릿

## 적용 규칙
- Telegram Bot API의 `parse_mode: HTML` 기준으로 작성
- 지원 태그: `<b>`, `<i>`, `<u>`, `<s>`, `<code>`, `<pre>`, `<a href="URL">`
- 텔레그램 메시지 길이 제한: 4,096자
- 이모지는 구분자 및 강조 목적으로만 사용

---

## 메시지 템플릿

```html
📡 <b>AI 동향 브리핑</b>
<i>{{PERIOD_FROM}} ~ {{PERIOD_TO}}</i>
━━━━━━━━━━━━━━━━━━━━━

🔍 <b>이번 주 TOP 3 이슈</b>
<i>관점: 발주사 리스크</i>

<b>① {{ISSUE_1_TITLE}}</b>
{{ISSUE_1_SUMMARY}}
⚠️ <i>리스크: {{ISSUE_1_RISK}}</i>
<a href="{{ISSUE_1_URL}}">원문 보기</a>

<b>② {{ISSUE_2_TITLE}}</b>
{{ISSUE_2_SUMMARY}}
⚠️ <i>리스크: {{ISSUE_2_RISK}}</i>
<a href="{{ISSUE_2_URL}}">원문 보기</a>

<b>③ {{ISSUE_3_TITLE}}</b>
{{ISSUE_3_SUMMARY}}
⚠️ <i>리스크: {{ISSUE_3_RISK}}</i>
<a href="{{ISSUE_3_URL}}">원문 보기</a>

━━━━━━━━━━━━━━━━━━━━━
⚖️ <b>이번 주 쟁점</b>

<b>{{DEBATE_TOPIC}}</b>

🟢 <i>{{VIEW_A_SOURCE}}</i>
{{VIEW_A_CONTENT}}

🔴 <i>{{VIEW_B_SOURCE}}</i>
{{VIEW_B_CONTENT}}

💡 <b>시사점</b>
{{DEBATE_INSIGHT}}

━━━━━━━━━━━━━━━━━━━━━
📊 <code>수집 {{TOTAL_COUNT}}건 분석 완료</code>
```

---

## 변수 명세

| 변수 | 설명 | 형식 |
|------|------|------|
| `{{PERIOD_FROM}}` | 수집 시작일 | MM/DD(요일) |
| `{{PERIOD_TO}}` | 수집 종료일 | MM/DD(요일) |
| `{{ISSUE_N_TITLE}}` | N번 이슈 제목 | 30자 이내 |
| `{{ISSUE_N_SUMMARY}}` | N번 이슈 요약 | 100자 이내 |
| `{{ISSUE_N_RISK}}` | N번 발주사 리스크 | 50자 이내 |
| `{{ISSUE_N_URL}}` | N번 원문 URL | URL |
| `{{DEBATE_TOPIC}}` | 쟁점 주제 | 40자 이내 |
| `{{VIEW_A_SOURCE}}` | A 시각 출처 | 매체명 |
| `{{VIEW_A_CONTENT}}` | A 시각 내용 | 80자 이내 |
| `{{VIEW_B_SOURCE}}` | B 시각 출처 | 매체명 |
| `{{VIEW_B_CONTENT}}` | B 시각 내용 | 80자 이내 |
| `{{DEBATE_INSIGHT}}` | 쟁점 시사점 | 100자 이내 |
| `{{TOTAL_COUNT}}` | 총 분석 기사 수 | 숫자 |

---

## 주의 사항

1. HTML 특수문자는 반드시 이스케이프: `&` → `&amp;`, `<` → `&lt;`, `>` → `&gt;`
2. URL에 특수문자 포함 시 percent-encoding 적용
3. 전체 메시지가 4,096자 초과 시 이슈 요약과 리스크를 각각 50자/30자로 축약
4. 원문 URL이 없는 경우 `<a href="">` 태그 제거하고 출처명만 표기
