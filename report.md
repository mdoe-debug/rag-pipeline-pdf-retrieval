# RAG Pipeline Project Report
**Course-End Project 1 — Building an RAG Pipeline Using PDF Chunking and Retrieval**

## Introduction

Retrieval-Augmented Generation (RAG) is an approach that combines a search step with a language model's generation step, allowing an AI assistant to answer questions using the actual content of specific documents rather than relying solely on its general training data. This is especially valuable for long or specialized documents, where a user needs precise, source-grounded answers instead of generic responses.

The objective of this project was to design and build a complete RAG pipeline from scratch: load multiple PDF documents, split them into manageable chunks, convert those chunks into vector embeddings, store the embeddings in a FAISS vector database, and retrieve the most relevant content in response to a natural language query.

## Implementation Overview

The pipeline was built in Visual Studio Code using Python, with a dedicated virtual environment to manage dependencies. The following tools and libraries were used:

- Visual Studio Code — primary development environment and Jupyter notebook interface
- Python 3.14 — programming language for the entire pipeline
- LangChain (langchain, langchain-community, langchain-openai, langchain-text-splitters, langchain-classic) — document loading, text splitting, and retrieval chain orchestration
- PyPDFLoader — PDF parsing and page extraction
- RecursiveCharacterTextSplitter — chunking of page text into smaller overlapping segments
- OpenAI API (text-embedding model + gpt-3.5-turbo) — embedding generation and answer generation
- FAISS (faiss-cpu) — vector storage and similarity search
- python-dotenv — secure loading of the API key from a local .env file

Three PDF documents were used as the test dataset:

1. 7-Agentforce-Lessons-from-15-Enterprise-Deployments.pdf (23 pages)
2. IDC AI Market place report.pdf (14 pages)
3. wp-how-to-champion-ai-as-an-executive.pdf (8 pages)

The pipeline followed six sequential steps: (1) load environment variables and API credentials, (2) load and read all PDF pages, (3) split the pages into overlapping text chunks, (4) generate embeddings for each chunk and store them in a FAISS index, (5) run a semantic retrieval query through a RetrievalQA chain, and (6) display the specific chunks retrieved to support the generated answer.

## Metrics

| Metric | Value |
|---|---|
| Number of PDF files used | 3 |
| Total pages loaded | 45 |
| Total chunks created | 188 |
| Average chunk size | 413.5 characters |
| Chunk size / overlap settings | 500 characters / 50 characters |
| Embedding model | OpenAI text-embedding (via OpenAIEmbeddings) |
| Generation model | gpt-3.5-turbo |
| Retriever setting | Top-k = 3 |

### Sample Query and Result

**Query:** "What are the key lessons for enterprise AI deployments?"

**Generated Answer:**

1. Investing in AI early can deliver quick wins and prepare teams for long-term transformation.
2. Research and experiment with AI solutions to understand costs, features, use cases, and scalability.
3. Engage with vendors to gain insight into different AI tools and solutions.
4. Experiment with pilot projects to uncover potential pitfalls like cultural resistance, workflow disruptions, or skill gaps.
5. Move from pilot projects to production by aligning workflows to deliver measurable outcomes.

**Top Retrieved Chunks:**

- Chunk 1 — wp-how-to-champion-ai-as-an-executive.pdf, page 4 (discusses investing in AI early to deliver quick wins and expose potential pitfalls)
- Chunk 2 — wp-how-to-champion-ai-as-an-executive.pdf, page 5 (discusses researching AI products and engaging vendors on cost, features, and scalability)
- Chunk 3 — 7-Agentforce-Lessons-from-15-Enterprise-Deployments.pdf, page 3 (discusses moving from pilot to production with aligned enterprise workflows)

The retrieved chunks directly support each point in the generated answer, confirming that the retrieval step is correctly identifying semantically relevant content rather than returning arbitrary text.

## Conclusion

### Insights Gained

Building this pipeline end-to-end clarified how each stage of RAG contributes to answer quality. Chunk size and overlap directly affect retrieval precision: chunks that are too large dilute relevance, while chunks that are too small lose context. The pipeline also demonstrated that retrieval grounding works as intended — the GPT-generated answer was traceable to specific, verifiable source passages rather than being a generic response.

### Issues Faced

- LangChain's rapid version changes meant that some module paths (text_splitter, chains) had moved since older tutorials and documentation were written, requiring updated imports (langchain_text_splitters and langchain_classic.chains).
- PowerShell's default execution policy initially blocked activation of the Python virtual environment and required a one-time policy change (Set-ExecutionPolicy RemoteSigned).
- Care was needed to keep the OpenAI API key secure by isolating it in a .env file rather than hardcoding it into the notebook.

### Possible Improvements

- Experiment with different chunk sizes and overlap values to measure their effect on retrieval accuracy.
- Add a re-ranking step after initial retrieval to further improve the relevance of the top results.
- Extend the pipeline to support additional file types (Word documents, HTML, plain text) beyond PDF.
- Add automated evaluation (e.g., comparing retrieved chunks against a labeled set of expected answers) to quantitatively measure retrieval quality over time.