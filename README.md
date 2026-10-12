# Agentic RAG course-end project

## Run in your lab
1. Extract all files into one directory. Use Python 3.11/3.12.
2. Open agentic_rag_project.ipynb in Jupyter.
3. Install requirements using `%pip install -r requirements.txt`; restart the kernel.
4. Choose OpenAI or Azure in the configuration cell (or `.env`). Enter keys through password prompts. Tavily needs a separate API key. For Azure, use your lab-provided deployment, endpoint and API version. Never share keys.
5. Run cells in order. A fictional DevOps handbook PDF is generated and indexed automatically. Local embeddings need an initial model download.
6. Execute the three demo questions; inspect answers, citations and trace table. Save the executed notebook and submit it with this README.

## Architecture and rationale
Router Agent → structured validated decision → Python invokes PDFSearchTool or TavilySearchResults → Retriever Agent synthesizes evidence → answer and JSONL trace. Optional generation skips retrieval and is labeled as model knowledge.

CrewAI provides role-based Agent/Task execution and typed router output. The coordinator explicitly passes the router decision and retrieved evidence to the second agent and enforces the chosen retrieval path. Separate sequential crews allow conditional tool dispatch between agent tasks. PDFSearchTool performs vector retrieval; TavilySearchResults returns web content and URLs. Python manages logging and errors rather than relying on agent promises to use a tool.

## Responsibilities
Router: classify questions based on corpus coverage and freshness, rewrite query, provide a short routing rationale.
Retriever: answer using selected evidence, cite actual sources, disclose insufficient evidence.
Coordinator: validate the route, invoke the selected tool, pass outputs, log events and surface failures.

## Traceability and evaluation
`logs/trace.jsonl` captures the question, routing rationale, tool start/completion, source identifiers, answer and elapsed time. The notebook displays an event table and writes a CSV. These are decision summaries and interactions, not hidden chain-of-thought. PDF citations use filenames because page metadata is not guaranteed. Review source links manually; presence of a citation does not prove correctness.

Demonstration covers PDF rollback policy, current Kubernetes announcements through web, and general container explanation. Route/source assertions accompany manual grounding checks. Try a fact absent from the PDF to verify abstention. `logs/requirements-lock.txt` records installed versions after the live demo.

## Challenges and trade-offs
API access, package compatibility, local model download and Azure deployment configuration are environment-dependent. Local embeddings avoid embedding API credentials but require memory/disk and an initial download. TavilySearchResults is used because the assignment names it; newer LangChain examples may use another Tavily class. Use current CrewAI PDF config (`embedding_model` and `vectordb`), not older Embedchain examples. Pin successful versions from the generated lock file for reproducibility.

Search snippets can be incomplete. Single-source routing does not handle combined PDF/web questions fully. PDFs must contain extractable text; scanned files need OCR before indexing. Retrieval failures stop the run instead of silently producing ungrounded answers. Logs contain user questions and answers: use public training content.

## Verification status
Notebook code cells were syntax checked during preparation. Live LLM, PDF vector retrieval, and Tavily calls have NOT been executed in the preparation environment, which has no supplied credentials or CrewAI installation. Execute all demo cells in your lab before submission; empty outputs are not a completed working demo.

## References
https://docs.crewai.com/en/tools/file-document/pdfsearchtool
https://docs.crewai.com/en/concepts/llms
https://reference.langchain.com/python/langchain-community/tools/tavily_search/tool/TavilySearchResults
