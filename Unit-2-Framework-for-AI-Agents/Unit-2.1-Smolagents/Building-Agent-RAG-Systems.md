# 🧠 Building Agentic RAG Systems

<a href="./retrieval_agents.ipynb" target="_blank">
  <img src="https://img.shields.io/badge/Notebook-Open%20.ipynb-blue?style=for-the-badge&logo=jupyter" alt="Notebook Button"/>
</a>

Agentic RAG (Retrieval-Augmented Generation) **combines retrieval, generation, and autonomous agent capabilities** to improve information synthesis and reasoning.

## 🔍 What is RAG?

**Retrieval Augmented Generation (RAG)** systems combine the capabilities of data retrieval and generation models to provide context-aware responses.
Retrieval-Augmented Generation (RAG) = Query ➝ Retrieve ➝ Generate

**Retrieval-Augmented Generation (RAG)** integrates:
- **Search/Retrieval** (e.g., search engines)
- **LLM Generation**, using retrieved content for responses.

**Example**: A query is first used to fetch relevant info, which is then passed to an LLM to generate a context-rich response.

---

## 🤖 What is *Agentic* RAG?

Agentic RAG enhances traditional RAG with:
- Autonomous agents
- Dynamic query reformulation
- Multi-step reasoning & retrieval
- Result critique & synthesis

### 🔄 Key Differences from Traditional RAG:
| Traditional RAG                | Agentic RAG                                      |
|-------------------------------|--------------------------------------------------|
| Single-step retrieval         | Multi-step retrieval & refinement                |
| Passive semantic search       | Active query generation & critique               |
| Limited control over tools    | Intelligent tool selection & execution           |

---

## 🌐 Example: DuckDuckGo Web Agent

A simple agent using `DuckDuckGoSearchTool` to search and synthesize responses.

```python
from smolagents import CodeAgent, DuckDuckGoSearchTool, HfApiModel

search_tool = DuckDuckGoSearchTool()
model = HfApiModel()

agent = CodeAgent(model=model, tools=[search_tool])

response = agent.run(
    "Search for luxury superhero-themed party ideas, including decorations, entertainment, and catering."
)
print(response)
```

### Agent Workflow:
1. **Analyzes the Request** query intent (luxury + superhero party)
2. **Performs Retrieves** web data via DuckDuckGo
3. **Synthesizes Information** a full party plan
4. **Stores for Future Reference** results for future reuse

---

## 📚 Example: Custom Knowledge Base Tool

Using a vector database and BM25 retriever to search structured knowledge.

### Tool: `PartyPlanningRetrieverTool`
- Built using `BM25Retriever` and `RecursiveCharacterTextSplitter`
- Supports semantic search on party ideas

```python
from langchain.docstore.document import Document
from langchain.text_splitter import RecursiveCharacterTextSplitter
from smolagents import Tool
from langchain_community.retrievers import BM25Retriever
from smolagents import CodeAgent, HfApiModel

class PartyPlanningRetrieverTool(Tool):
    name = "party_planning_retriever"
    description = "Uses semantic search to retrieve relevant party planning ideas for Alfred’s superhero-themed party at Wayne Manor."
    inputs = {
        "query": {
            "type": "string",
            "description": "The query to perform. This should be a query related to party planning or superhero themes.",
        }
    }
    output_type = "string"

    def __init__(self, docs, **kwargs):
        super().__init__(**kwargs)
        self.retriever = BM25Retriever.from_documents(
            docs, k=5  # Retrieve the top 5 documents
        )

    def forward(self, query: str) -> str:
        assert isinstance(query, str), "Your search query must be a string"

        docs = self.retriever.invoke(
            query,
        )
        return "\nRetrieved ideas:\n" + "".join(
            [
                f"\n\n===== Idea {str(i)} =====\n" + doc.page_content
                for i, doc in enumerate(docs)
            ]
        )

# Simulate a knowledge base about party planning
party_ideas = [
    {"text": "A superhero-themed masquerade ball with luxury decor, including gold accents and velvet curtains.", "source": "Party Ideas 1"},
    {"text": "Hire a professional DJ who can play themed music for superheroes like Batman and Wonder Woman.", "source": "Entertainment Ideas"},
    {"text": "For catering, serve dishes named after superheroes, like 'The Hulk's Green Smoothie' and 'Iron Man's Power Steak.'", "source": "Catering Ideas"},
    {"text": "Decorate with iconic superhero logos and projections of Gotham and other superhero cities around the venue.", "source": "Decoration Ideas"},
    {"text": "Interactive experiences with VR where guests can engage in superhero simulations or compete in themed games.", "source": "Entertainment Ideas"}
]

source_docs = [
    Document(page_content=doc["text"], metadata={"source": doc["source"]})
    for doc in party_ideas
]

# Split the documents into smaller chunks for more efficient search
text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,
    chunk_overlap=50,
    add_start_index=True,
    strip_whitespace=True,
    separators=["\n\n", "\n", ".", " ", ""],
)
docs_processed = text_splitter.split_documents(source_docs)

# Create the retriever tool
party_planning_retriever = PartyPlanningRetrieverTool(docs_processed)

# Initialize the agent
agent = CodeAgent(tools=[party_planning_retriever], model=HfApiModel())

# Example usage
response = agent.run(
    "Find ideas for a luxury superhero-themed party, including entertainment, catering, and decoration options."
)

print(response)
```

### Features:
- Splits and indexes documents
- Retrieves top ideas by semantic relevance
- Synthesizes a refined party plan

---

## 🚀 Enhanced Retrieval Strategies

Agentic RAG agents can:

- **Query Reformulation**: Instead of using the raw user query, the agent can craft optimized search terms that better match the target documents
- **Multi-Step Retrieval**: The agent can perform multiple searches, using initial results to inform subsequent queries
- **Source Integration**: Information can be combined from multiple sources like web search and local documentation
- **Result Validation**: Retrieved content can be analyzed for relevance and accuracy before being included in responses

---

## ✅ Best Practices for Agentic RAG

- Choose tools dynamically based on the query
- Use **memory** to maintain conversation context
- Have **fallback mechanisms** for failed retrievals
- Implement **validation steps** for retrieved data

---

## 📘 Resources

- [Agentic RAG: turbocharge your RAG with query reformulation and self-query! 🚀](https://huggingface.co/learn/cookbook/agent_rag)
