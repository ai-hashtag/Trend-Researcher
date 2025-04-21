# Trend Researcher

트렌드 키워드 기반 해시태그를 자동 수집·저장하고, 유사 키워드 추천 및 다운로드까지 지원하는 AI 기반 SNS 분석 도구입니다.

## 주요 기능

- 사용자 입력 주제에 따른 트렌디 해시태그 생성
- Sentence-BERT 임베딩 기반 유사도 분석
- ChromaDB 벡터 DB를 활용한 해시태그 저장
- Streamlit 기반 사용자 인터페이스 제공
- 클립보드 복사 / CSV 다운로드 지원

## 시스템 구성

| 구성 요소              | 설명 |
|-----------------------|------|
| `TrendResearcher`     | 해시태그 수집 + 중복 제거 + 저장 |
| `SentenceTransformer` | 문장 임베딩 생성 (paraphrase-MiniLM-L6-v2) |
| `ChromaDB`            | 벡터 기반 해시태그 저장소 |
| `Streamlit`           | 인터랙티브 UI 구성 |
| `pyngrok` (Colab용)   | 브라우저 접속 URL 생성 |

uv pip install streamlit sentence-transformers chromadb pandas pyperclip
streamlit run trend_researcher_app.py
