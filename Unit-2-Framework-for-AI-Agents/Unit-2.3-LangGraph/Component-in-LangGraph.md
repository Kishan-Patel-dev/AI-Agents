# 🧱 Building Blocks of LangGraph

To build structured LLM workflows using **LangGraph**, you need to understand its four core components: **State**, **Nodes**, **Edges**, and **StateGraph**.

- An application in LangGraph starts from an **entrypoint**, and depending on the execution, the flow may go to one function or another until it reaches the END.

1[Application in LangGraph](../../assets/application.png)

---

## 1️⃣ State

The **State** is the **central data structure** that flows through the application and is updated at each step.

```python
from typing_extensions import TypedDict

class State(TypedDict):
    graph_state: str
```

* ✅ **User-defined**: You define to contain all data needed for decision-making process!
> 💡 Tip: Think carefully about what information your application needs to track between steps.


---

## 2️⃣ Nodes

Nodes are **Python functions** that take in the state, perform an operation, and return updates to the state.

```python
def node_1(state):
    return {"graph_state": state["graph_state"] + " I am"}

def node_2(state):
    return {"graph_state": state["graph_state"] + " happy!"}

def node_3(state):
    return {"graph_state": state["graph_state"] + " sad!"}
```

### Node Use Cases:

* 🔮 LLM calls
* 🧰 Tool integrations
* 🔀 Conditional logic
* 👤 Human-in-the-loop steps

> ℹ️ `START` and `END` nodes are built-in by LangGraph.

---

## 3️⃣ Edges

**Edges** define how the application moves from one node to the next.

### 🔁 Types of Edges:

* **Direct**: Unconditional transition
* **Conditional**: Based on logic or LLM output

```python
import random
from typing import Literal

def decide_mood(state) -> Literal["node_2", "node_3"]:
    if random.random() < 0.5:
        return "node_2"
    return "node_3"
```

---

## 4️⃣ StateGraph

The **StateGraph** is the container where you define your entire agent's workflow.

```python
from langgraph.graph import StateGraph, START, END

builder = StateGraph(State)
builder.add_node("node_1", node_1)
builder.add_node("node_2", node_2)
builder.add_node("node_3", node_3)

builder.add_edge(START, "node_1")
builder.add_conditional_edges("node_1", decide_mood)
builder.add_edge("node_2", END)
builder.add_edge("node_3", END)

graph = builder.compile()
```

### 🔍 Visualization

```python
from IPython.display import Image, display
display(Image(graph.get_graph().draw_mermaid_png()))
```

![Visulization of Graph](../../assets/basic_graph.jpeg)

### ⚙️ Execution

```python
graph.invoke({"graph_state": "Hi, this is Lance."})
# Output Example:
# ---Node 1---
# ---Node 3---
# {'graph_state': 'Hi, this is Lance. I am sad!'}
```

---

## ✅ Summary

| Component    | Description                                     |
| ------------ | ----------------------------------------------- |
| `State`      | Tracks data across nodes                        |
| `Nodes`      | Functions that update state                     |
| `Edges`      | Define path through the graph (direct or logic) |
| `StateGraph` | Blueprint of the entire agent workflow          |

---

LangGraph’s modular design allows you to build **structured**, **traceable**, and **production-ready** LLM workflows with ease.
