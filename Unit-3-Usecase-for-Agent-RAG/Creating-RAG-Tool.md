# 🧠 Creating a RAG Tool for Guest Stories

> Let’s help Alfred—the gala’s AI host—recall guest details in real time using a custom Retrieval-Augmented Generation (RAG) tool built with your own dataset.

---

## 🎯 Why Use RAG for the Gala?

Alfred needs to recall personal guest info like:

* Names and relations to the host
* Unique bios and fun facts
* Contact details (email)
* Up-to-date or customized information

Traditional LLMs can’t reliably do this, but RAG enables dynamic, specific, and accurate recall using custom datasets.

---

## 🗂️ Dataset Overview

We use the `agents-course/unit3-invitees` dataset, which includes:

* **Name**
* **Relation** (to the host)
* **Description** (bio or story)
* **Email Address**

This can be expanded with dietary preferences, taboo topics, gift ideas, etc.

---

## 🏗️ Project Setup

We structure our app like a real-world deployable project:
[HF Space](https://huggingface.co/spaces/agents-course/Unit_3_Agentic_RAG)

```
retriever.py     # Defines retrieval logic
tools.py         # Hosts Alfred’s tools
app.py           # The agent entry point
```

---

## 🧱 Step-by-Step Build

### ✅ Step 1: Load and Prepare the Dataset

We will use the Hugging Face `datasets` library to load the dataset and convert it into a list of `Document` objects from the `llama_index.core.schema` module.

- Load the dataset
- Covert the each info into a `Document` object with formatted content
- Store the `Document` objects in a list

```python
import datasets
from llama_index.core.schema import Document

# Load the dataset
guest_dataset = datasets.load_dataset("agents-course/unit3-invitees", split="train")

# Convert dataset entries into Document objects
docs = [
    Document(
        text="\n".join([
            f"Name: {guest_dataset['name'][i]}",
            f"Relation: {guest_dataset['relation'][i]}",
            f"Description: {guest_dataset['description'][i]}",
            f"Email: {guest_dataset['email'][i]}"
        ]),
        metadata={"name": guest_dataset['name'][i]}
    )
    for i in range(len(guest_dataset))
]
```

---

### 🛠️ Step 2: Create the Retriever Tool

Use `BM25Retriever` from the llama_index.retrievers.bm25 module to create a retriever tool.

- `docstring` help the agnt understnad when and how to use this tool
- The type decorators define what parameters the tools expects.
- `BM25Retriever` is a powerful text retrieval algorithm that does't require embeddings.
- The method processes the query and returns the most relevant guest information.

```python
from llama_index.core.tools import FunctionTool
from llama_index.retrievers.bm25 import BM25Retriever

bm25_retriever = BM25Retriever.from_defaults(nodes=docs)

def get_guest_info_retriever(query: str) -> str:
    """Retrieves detailed information about gala guests based on their name or relation."""
    results = bm25_retriever.retrieve(query)
    if results:
        return "\n\n".join([doc.text for doc in results[:3]])
    else:
        return "No matching guest information found."

# Initialize the tool
guest_info_tool = FunctionTool.from_defaults(get_guest_info_retriever)
```

---

### 🤖 Step 3: Integrate the Tool with Agent

Create Agent using `CodeAgent` and enable it to respond using the guest tool:

- Initialize a Hugging Face model using the `HuggingFaceInferenceAPI` class
- Create Agent as `AgentWorkflow`, including tool
- Retrieve information
```python
from llama_index.core.agent.workflow import AgentWorkflow
from llama_index.llms.huggingface_api import HuggingFaceInferenceAPI

# Initialize the Hugging Face model
llm = HuggingFaceInferenceAPI(model_name="Qwen/Qwen2.5-Coder-32B-Instruct")

# Create Alfred, our gala agent, with the guest info tool
alfred = AgentWorkflow.from_tools_or_functions(
    [guest_info_tool],
    llm=llm,
)

# Example query Alfred might receive during the gala
response = await alfred.run("Tell me about our guest named 'Lady Ada Lovelace'.")

print("🎩 Alfred's Response:")
print(response)
```

---

## 💬 Example Interaction

**You:** “Alfred, who’s that gentleman talking to the ambassador?”
**Alfred:** “That’s Dr. Nikola Tesla... He’s passionate about pigeons—great conversation starter!”

```json
{
  "name": "Dr. Nikola Tesla",
  "relation": "old friend from university days",  
  "description": "He's patented a wireless energy system... passionate about pigeons.",
  "email": "nikola.tesla@gmail.com"
}
```

---

## 🚀 Taking It Further

Enhance Alfred with:

* 🔍 Improve the retriever to use a more sophisticated algorithm like [sentence-transformers](https://www.sbert.net/)
* 🧠 Conversation memory
* 🌐 Real-time info (web search integration)
* 📚 Multi-index knowledge access
* 💡 Conversation starters based on interests

> 📌 Tip: Try modifying the tool to return conversation openers related to each guest’s interests.
