# docusense-RAG
Smart search and Q&amp;A system within the context of the documentation you are browsing.

---
## What It Does
- Goes beyond summarization by acting as a **context-aware search engine**.  
- Uses the site’s structure as its knowledge base to provide precise answers.  
- Example:  
  - You're on the Next.js `getServerSideProps` documentation.  
  - You ask: *“How do I implement the context API with TypeScript?”*  
  - Instead of a generic search, DocuSense retrieves the answer directly from the **Next.js docs** (including linked TypeScript guides).
---

## The Contextualization

Context isn’t just text on the current page—it’s the entire section or documentation set the user is currently on.  

### Strategies for Defining Context
1. **URL Pattern Matching (Simple)**  
   - Example: On `https://nextjs.org/docs/pages/api-reference/functions/get-server-side-props`,  
     context = `https://nextjs.org/docs/`.  
   - Any link starting with this base URL becomes part of the contextual knowledge base.  
   - Works for most structured docs.

2. **Sitemap & Link Analysis (Advanced)**  
   - Checks for `/sitemap.xml` at the root.  
   - Parses and builds a map of the docs (or spiders up to N depth).  
   - More powerful but computationally heavier.

3. **User-Defined Context (Flexible)**  
   - The UI could have a toggle: "Context: This Page | This Section | Entire Docs":  
     - **This Page** → Only current page text  
     - **This Section (default)** → URL-based section grouping to define a section
     - **Entire Docs** → Sitemap-driven ingestion  

> Needs robust error handling: no sitemap, malformed links, fuzzy, real-world inputs, empty pages.

---

## The "Search for Help" (RAG in a Browser)
Essentially a **mini Retrieval-Augmented Generation (RAG)** system inside a browser extension.

### A couple things to note while we are here:
- **Performance** → Must chunk and embed text before querying an LLM.  
- **Cost Efficiency** → Minimize tokens sent to LLM API.  
- **Accuracy** → Ground responses in retrieved doc chunks with citations/sources.

---

## Proposed Tech Stack
- **Frontend (Popup/Options UI)** → Next.js (App Router) or React + Vite, with `shadcn/ui`.  
- **Chrome Extension APIs** → scripting, tabs, runtime, storage.  
- **Background Process/Service Worker** → JavaScript (ES6+), deps bundled with Webpack/Rollup.  
- **AI & Search**:  
  - **Embeddings**: Local (e.g., Google's Universal Sentence Encoder Lite via TensorFlow.js) or free APIs (Cohere Embed).  
  - **LLM for synthesis**: Google Gemini API (free, generous, and high quality).  
  - **Storage**: `chrome.storage.local` to cache embeddings and pre-crawled content for repeated visits.  

---

## Edge Cases & Considerations
- Handle API rate limits gracefully (track timestamps, notify users).  
- Fallback if:  
  - Page has no text  
  - Sitemap is malformed  
  - LLM API is down  
- Possible **local mode** with smaller models (Transformers.js) for sensitive workflows.  
- UX:  
  - Render answers in clean Markdown.  
  - Include inline citations with links (e.g., *Source: [Next.js Docs > API Reference > Data Fetching](https://nextjs.org/docs/)*).  

---
