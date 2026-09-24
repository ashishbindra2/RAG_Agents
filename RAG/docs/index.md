# RAG

R => Retrive (FIND USEFUL RESPONSE)
A => Augument(Add it to AI knowlegde)
G => Genrate (Create a better resposnse)

---

## Encoding decording

will cover later

---

1. Retriveal component
    Retrive data assist in genrate accurate response

    - Query Encoding: convert input Query into vector repurative
    - Passge Encoding: encode doucment into vectors ad store thaem for dast reteival
    - Reterval: Compare the query vector with stored vector to find the most relevent passage.\

2. Generative component

Reterival data pass to generative model and give the final response

- FiD (fusion in decoder) slow,complex, focuss in decoding, reterive and genrate seprateHigh Qulaity
- FiE (Fusion-in-Encoder) fast,simple, fussion happening at encoding befor dead, reterive and genrate togather, Quick reponse genrate

## indexing

it means storing your documents as vectors so you cansearch them by meaning  instead of extact keywords

work in 2 phases :

1. split your document into chunks send each chunk to your llm embedding and get vector back which is list of numbers. you store those vectors aloong with original text and any metadata.
2. when user search first embedded the query and store find the vectores closest to it

indexing is the datastrucure that makes that "find the closest vectore" steps faster. these are 3 types

1. Flat
2. IVF
3. HBSW(Chroma, qurant,FAISS,pgvector)

## Vector DB list

- Qdrant
- pgvector
- chromadb
- pincone
- weaviate

## Vector Embeding

- text-embeding-3-small
- Qwen3-emebding-0.6
- openai/clip-path32 (img)
- openai/clip-path16 (img)
- BAII/en-vis

## Memory in agent/LLM

1million token => [model] stateless

type of memory

- (Factual memory) short term memory (STM) Hold for session: whilein session the task is getting perform
- (Episodic memory) Long term memory (LTM) Always: Name, Age
- (sementanic memory)

## Conversessional AI

### 1. STM (short term Memory)

- The information about ongoing session.
- History of current conversion

### 2. LTM (Long Term Memory)

MongoDb,QQdrant,Graph

1. Factual memory
2. Episodic Memory
3. Semetic Memory

---

1. Factual Memory

    - Fact about user alway want in memory
    - Facts like name,age...
    - This is something always in the context

    small chunk of your memory

    - communate style

2. Episodic Memory

    Rememebrs specifics past interaction or outcomes

3. Sementic Memory

 Storage generic data
captial of india...

### To load the pdf (PyPDFLoader)

- first need to install PyPDFLoader
- `pip install -U langchain-community pypdf`

```py
from langchain_community.document_loaders import PyPDFLoader    
pdf_path = Path(__file__).parent.parent / "data/PythonTutorial_3.7.pdf"

## Load the PDF file in python program
loader = PyPDFLoader(file_path=str(pdf_path))
```

### Load pdf and return page by page

```py
docs = loader.load()

for doc in docs:
    print(doc)
```

### Chunking : split the docks into smaller cchunks of text

`pip install langchain-text-splitters`

```py
text_splitter = RecursiveCharacterTextSplitter(chunk_size=1000, chunk_overlap=200)
chunks = text_splitter.split_documents(docs)
print(chunks[0])
```

## Embedding

- pip install -U langchain-openai

```py
from langchain_openai import OpenAIEmbeddings

# Access the variables
Model = os.getenv("MODEL_PATH")
MODEL_NAME= os.getenv("MODEL_NAME")

## vector emebdding from this chunked text
## connect LangChain to your LLM
embeddings = OpenAIEmbeddings(
    model=MODEL_NAME, base_url=Model, api_key="EMPTY", check_embedding_ctx_length=False
)

## test embeddings
texts = [
    "AI is a field of computer science.",
    "ML is a subset of AI.",
    "Python is a programing language",
]

vectors = embeddings.embed_documents(texts)
print(vectors)
```

pip install langchain-qdrant qdrant-client

## Vector simlarity seacrh

```py
vector_store = QdrantVectorStore.from_existing_collection(
    collection_name="python_tutorial",
    embedding=embedding_model,
    url=clustor,
    api_key=api_key,
)


user_query = input("Enter your query: ")

# similarity search (typical rag retrival)
results = vector_store.similarity_search(query=user_query, k=5)
# get reterival chunks from vector DB
for r in results:
    print(r.metadata)

context = "\n".join(
    [
        f"Page Content: {r.page_content}\nPage Number: {r.metadata.get('page_number','uknowns')}"
        for r in results
    ]
)

```

## response

```py
SYSTEM_PROMPT = f"""
You are a helpful AI assistant that answers user questions based on the context retrived from  a PDF file along with page_counts and page_number.
You should only answer the user's question based on the  following context and navigate the user yo open the right page number to know more
CONTEXT: {context}
"""

response = openai_client.chat.completions.create(
    model="qwen3-coder",
    messages=[
        {"role": "system", "content": SYSTEM_PROMPT},
        {"role": "user", "content": user_query},
    ],
)

print("AI RESPONSE")
print(response.choices[0].message.content)
```
