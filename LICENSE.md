!pip install -q streamlit sentence-transformers chromadb pandas pyperclip pyngrok

code = '''
import streamlit as st
import pandas as pd
import pyperclip
import io
from datetime import datetime
from sentence_transformers import SentenceTransformer
import chromadb
from chromadb.config import Settings

model = SentenceTransformer('paraphrase-MiniLM-L6-v2')
client = chromadb.Client(Settings())

def dummy_trending_hashtags(topic):
    return [f"#{topic}그램", f"#{topic}스타그램", "#감성사진", "#일상기록", "#트렌드"]

class TrendResearcher:
    def run(self, topic):
        print(f"[TrendResearcher] '{topic}' 관련 트렌드 해시태그 수집 중...")
        hashtags = dummy_trending_hashtags(topic)
        self.store_hashtags(hashtags, category=topic)

    def store_hashtags(self, hashtags, category="default"):
        collection_name = f"trend_{category.lower()}"
        collection = client.get_or_create_collection(name=collection_name)

        existing_docs = collection.get()["documents"]
        unique_hashtags = list(set(hashtags) - set(existing_docs))

        if not unique_hashtags:
            return []

        timestamp = datetime.now().isoformat()
        embeddings = model.encode(unique_hashtags)
        for tag, vec in zip(unique_hashtags, embeddings):
            collection.add(
                documents=[tag],
                ids=[tag],
                embeddings=[vec],
                metadata={"saved_at": timestamp}
            )

        print(f"[TrendResearcher] [{category}] 중복 제외 후 {len(unique_hashtags)}개 해시태그 저장 완료")

    @property
    def client(self):
        return client

agent = TrendResearcher()

st.set_page_config(page_title="Trend Researcher", layout="centered")
st.title("Trend Researcher Agent")
st.markdown("주제를 입력하면 관련 트렌드 해시태그를 수집하고 저장합니다.")

col1, col2 = st.columns(2)
with col1:
    topic = st.text_input("트렌드 주제", value="카페")
with col2:
    category = st.selectbox("카테고리 선택", ["일상", "여행", "음식", "패션", "기타"], index=0)

if st.button("해시태그 수집 실행"):
    with st.spinner("수집 중입니다..."):
        agent.run(topic=topic)
    st.success("수집 완료! 아래에서 저장된 해시태그를 확인하세요.")

collection_name = f"trend_{category.lower()}"
collection = agent.client.get_or_create_collection(name=collection_name)
data = collection.get()

if data["documents"]:
    st.subheader(f"'{category}' 카테고리의 저장된 해시태그")
    df = pd.DataFrame({
        "Hashtag": data["documents"],
        "Saved At": [md.get("saved_at", "N/A") for md in data["metadatas"]]
    }).sort_values(by="Saved At", ascending=False)

    st.dataframe(df, use_container_width=True)

    all_hashtags = " ".join(df["Hashtag"].tolist())
    if st.button("클립보드에 복사"):
        try:
            pyperclip.copy(all_hashtags)
            st.success("클립보드에 복사 완료")
        except Exception:
            st.warning("Colab 환경에서는 클립보드 복사가 지원되지 않습니다.")

    csv_buffer = io.StringIO()
    df.to_csv(csv_buffer, index=False)
    st.download_button(
        label="CSV로 다운로드",
        data=csv_buffer.getvalue(),
        file_name=f"{collection_name}_hashtags.csv",
        mime="text/csv"
    )
else:
    st.warning(f"'{category}' 카테고리에 저장된 해시태그가 아직 없습니다.")
'''

with open("trend_researcher_app.py", "w") as f:
    f.write(code)

from pyngrok import ngrok

!streamlit run trend_researcher_app.py &

public_url = ngrok.connect(port='8501')
print(f"[Streamlit 앱 바로가기] → {public_url}")
