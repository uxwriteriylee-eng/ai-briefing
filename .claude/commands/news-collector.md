# news-collector 서브 에이전트

AI·인공지능·AX 관련 뉴스를 지정 기간 동안 수집하여 JSON으로 저장한다.

## 입력 파라미터

이 커맨드는 오케스트레이터로부터 아래 값을 전달받는다:
- `keywords`: 검색 키워드 목록 (기본값: AI, 인공지능, AX)
- `date_from`: 수집 시작일 (YYYY-MM-DD)
- `date_to`: 수집 종료일 (YYYY-MM-DD)

## 수집 단계

### 1. 날짜 범위 확인
입력된 date_from ~ date_to가 유효한 범위인지 확인한다.
- date_from이 date_to보다 늦으면 오류 처리
- 미래 날짜가 포함되면 경고 후 오늘까지만 수집

### 2. 키워드별 뉴스 검색
각 키워드("AI", "인공지능", "AX")에 대해 WebSearch 도구를 사용하여 뉴스를 검색한다.

검색 쿼리 형식:
```
{키워드} 뉴스 after:{date_from} before:{date_to}
```

수집 대상 항목:
- 제목 (title)
- 출처 (source)
- URL
- 발행일 (published_at)
- 본문 요약 (200자 이내)
- 매칭된 키워드 (keywords_matched)

### 3. 중복 제거
동일 URL 또는 제목 유사도 80% 이상인 기사는 하나만 유지한다.

### 4. JSON 저장
수집 결과를 아래 경로에 저장한다:
```
./reports/collected/{date_from}.json
```

JSON 스키마:
```json
{
  "collected_at": "ISO8601 타임스탬프",
  "period": {
    "from": "YYYY-MM-DD",
    "to": "YYYY-MM-DD"
  },
  "keywords": ["AI", "인공지능", "AX"],
  "articles": [
    {
      "id": "순번 (001부터)",
      "title": "기사 제목",
      "source": "매체명",
      "url": "기사 URL",
      "published_at": "ISO8601",
      "summary": "200자 이내 요약",
      "keywords_matched": ["매칭된 키워드 목록"]
    }
  ],
  "total_count": 0
}
```

## 성공/실패 기준

**성공**: `total_count >= 1` 이고 JSON 파일 저장 완료
**실패 시 반환**:
```json
{
  "status": "error",
  "step": "news-collector",
  "error_message": "오류 내용",
  "keywords_searched": [],
  "date_range": { "from": "", "to": "" },
  "articles_found": 0
}
```
