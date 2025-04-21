Trend Researcher

AI 기반 SNS 트렌드 분석 도구  
사용자가 입력한 주제에 따라 최신 해시태그를 자동 수집하고, 유사 키워드를 추천하며 벡터 임베딩 기반으로 저장하고 관리할 수 있는 Streamlit 애플리케이션입니다.

## 주요 기능

- 주제 기반 해시태그 자동 수집 (`dummy` + 크롤링 연동 가능)
- 임베딩 기반 유사 키워드 추천 (Sentence-BERT 활용)
- 벡터 DB 저장 (ChromaDB, 중복 제거 포함)
- Streamlit UI 실행 (버튼 클릭으로 수집/추천 실행)
- 클립보드 복사 / CSV 다운로드 지원
- 세션 상태 관리 (`st.session_state`)
- Google Colab에서 실행 가능 (`pyngrok`으로 터널링 지원)

| 구성 요소              | 설명 |
|-----------------------|------|
| `TrendResearcher`     | 수집 및 DB 저장 담당 에이전트 |
| `SentenceTransformer` | 키워드 임베딩 생성 (MiniLM 모델 사용) |
| `ChromaDB`            | 해시태그 벡터 저장소 (컬렉션별 분류) |
| `Streamlit`           | 웹 UI 앱 실행 |
| `pyngrok`             | Colab에서 외부 접속 주소 생성용 |

```bash
uv venv --python 3.12
source .venv/bin/activate
uv pip install streamlit sentence-transformers chromadb pandas pyperclip
streamlit run trend_researcher_app.py

!pip install -q streamlit pyngrok sentence-transformers chromadb pandas pyperclip
with open("trend_researcher_app.py", "w") as f:
    f.write(open("YOUR_CODE_HERE").read())
from pyngrok import ngrok
!streamlit run trend_researcher_app.py &
public_url = ngrok.connect(8501)
print(public_url)

Trend-Researcher/
│
├── trend_researcher_app.py
├── agents/
│   └── trend_researcher.py     
├── requirements.txt
└── README.md

확장 계획

실시간 SNS 해시태그 크롤링 (snscrape, instaloader 등)
키워드 추천 알고리즘 고도화 (TF-IDF + BERT hybrid)
사용자 로그인 기반 데이터 저장소 분리
해시태그 트렌드 변화 시각화 (matplotlib/plotly)
클립보드 복사 기능 Colab 호환 개선
