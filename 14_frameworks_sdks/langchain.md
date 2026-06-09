# LangChain

LangChain is a framework for building LLM applications — chains, RAG pipelines, agents, and document processing.

## Core Concepts

### LCEL (LangChain Expression Language)
The modern way to build chains using the `|` pipe operator:
```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

llm = ChatOpenAI(model="gpt-4o")
prompt = ChatPromptTemplate.from_template("Summarize this: {text}")
parser = StrOutputParser()

chain = prompt | llm | parser
result = chain.invoke({"text": "LangChain is a framework..."})
```
Each `|` passes output of left to input of right.

### Document Loaders
Load text from any source:
```python
from langchain_community.document_loaders import PyPDFLoader, WebBaseLoader

loader = PyPDFLoader("report.pdf")
docs = loader.load()  # list of Document(page_content=..., metadata={...})
```

### Text Splitters
```python
from langchain.text_splitter import RecursiveCharacterTextSplitter

splitter = RecursiveCharacterTextSplitter(chunk_size=512, chunk_overlap=50)
chunks = splitter.split_documents(docs)
```

### Embeddings + Vector Stores
```python
from langchain_openai import OpenAIEmbeddings
from langchain_community.vectorstores import Chroma

embeddings = OpenAIEmbeddings()
vectorstore = Chroma.from_documents(chunks, embeddings)
retriever = vectorstore.as_retriever(search_kwargs={"k": 5})
```

### RAG Chain
```python
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.runnables import RunnablePassthrough

prompt = ChatPromptTemplate.from_template("""
Answer based only on the context below.
Context: {context}
Question: {question}
""")

def format_docs(docs):
    return "\n\n".join(d.page_content for d in docs)

rag_chain = (
    {"context": retriever | format_docs, "question": RunnablePassthrough()}
    | prompt
    | llm
    | StrOutputParser()
)

answer = rag_chain.invoke("What is the refund policy?")
```

### Agents (legacy)
```python
from langchain.agents import create_tool_calling_agent, AgentExecutor
from langchain_core.tools import tool

@tool
def get_weather(location: str) -> str:
    """Get weather for a city."""
    return f"25C, sunny in {location}"

agent = create_tool_calling_agent(llm, [get_weather], prompt)
executor = AgentExecutor(agent=agent, tools=[get_weather])
executor.invoke({"input": "Weather in Tokyo?"})
```
For complex agents, use LangGraph instead.

## When to Use LangChain
- RAG pipelines (its strongest use case)
- Document processing pipelines
- Quick prototyping with many built-in integrations
- When you need 100+ pre-built document loaders/tools

## When NOT to Use LangChain
- Simple single LLM calls (just use `openai` directly)
- Complex stateful agents (use LangGraph)
- Production systems where you need full control and no magic
