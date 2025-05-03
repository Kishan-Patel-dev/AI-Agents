# 🧠 Multi-Agent Systems with `smolagents`

<a href="./multiagent_notebook.ipynb" target="_blank">
  <img src="https://img.shields.io/badge/Notebook-Open%20.ipynb-blue?style=for-the-badge&logo=jupyter" alt="Notebook Button"/>
</a>

Multi-agent systems enable **specialized agents to collaborate on complex tasks**, improving **modularity**, **scalability**, and **robustness**.

In `smolagents`, different agents can be combined to generate Python code, call external tools, perform web searches, and more. By orchestrating these agents, we can create powerful workflows.

---

## 🚦 Core Concept

Instead of a monolithic AI, MAS uses:

- A **Manager Agent** for task delegation.
- A **Code Interpreter Agent** for executing logic/code.
- A **Web Search Agent** for retrieving external information.

These agents may utilize tools like:
- `DuckDuckGoSearchTool`
- `VisitWebpageTool`
- `calculate_cargo_travel_time` (custom utility)

---

## ⚙️ Real-World Example: "Find the Batmobile"

**Objective:**  
Help Alfred find a replacement Batmobile by identifying past Batman filming locations and estimating the **cargo plane transfer time** from Gotham (40.7128° N, 74.0060° W).

### 📍 Task Workflow

1. **Search for Batman filming locations**
2. **Calculate cargo plane transfer time** using Haversine formula.
3. **Find supercar factories** within similar transfer times.
4. **Visualize** on a map (colored by travel time).

### 📦 Package Setup

```bash
pip install 'smolagents[litellm]' plotly geopandas shapely kaleido -q
```

---

## 🔧 Custom Tool: `calculate_cargo_travel_time`

Python function to compute air travel time between coordinates, accounting for average cargo plane speed and real-world conditions.

```python
def calculate_cargo_travel_time(origin_coords, destination_coords, cruising_speed_kmh=750.0):
    # ... great-circle calculation ...
    return round(flight_time, 2)
```

---

## 🧪 Initial Agent Setup

```python
from smolagents import CodeAgent, GoogleSearchTool, VisitWebpageTool
from smolagents.models import HfApiModel

model = HfApiModel("Qwen/Qwen2.5-Coder-32B-Instruct", provider="together")

agent = CodeAgent(
    model=model,
    tools=[GoogleSearchTool("serper"), VisitWebpageTool(), calculate_cargo_travel_time],
    additional_authorized_imports=["pandas"],
    max_steps=20,
)

task = """Find all Batman filming locations, calculate transfer time to Gotham, and return as a dataframe. Also include supercar factories."""
result = agent.run(task)
```

---

## 🗺️ Sample Output (Partial)

| Location                                     | Travel Time (hrs) |
|---------------------------------------------|-------------------|
| Glasgow, UK (Necropolis Cemetery)           | 8.60              |
| Liverpool, UK (St. George's Hall)           | 8.81              |
| New York City (Wall Street)                 | 1.00              |
| Rajasthan, India (Mehrangarh Fort)          | 18.34             |
| Woking, UK (McLaren Factory)                | 9.13              |

---

## 📌 Optimization: Agent Planning

```python
agent.planning_interval = 4

detailed_report = agent.run(f"""
You're an expert analyst. You make comprehensive reports after visiting many websites.
Don't hesitate to search for many queries at once in a for loop.
For each data point that you find, visit the source url to confirm numbers.

{task}
""")

print(detailed_report)
```

---

## 🤝 Multi-Agent Collaboration

To improve performance and reduce token usage:
- Split into **Web Agent** and **Manager Agent**
- Each handles focused tasks and retains a smaller memory context

### 🔍 Web Agent

```python
web_agent = CodeAgent(
    model=model,
    tools=[GoogleSearchTool("serper"), VisitWebpageTool(), calculate_cargo_travel_time],
    name="web_agent",
    description="Browses the web to find information",
    verbosity_level=0,
    max_steps=10,
)
```

### 🧠 Manager Agent

Uses `DeepSeek-R1` with `planning_interval` and full visualization capabilities (`plotly`, `geopandas`, etc.).

---

## 🎨 Visualizations & Reasoning

After collecting data, the manager agent:
- Verifies reasoning
- Loads `saved_map.png`
- Uses multimodal models (like `gpt-4o`) for image + data understanding

```python
from smolagents.utils import encode_image_base64, make_image_url
from smolagents import OpenAIServerModel


def check_reasoning_and_plot(final_answer, agent_memory):
    multimodal_model = OpenAIServerModel("gpt-4o", max_tokens=8096)
    filepath = "saved_map.png"
    assert os.path.exists(filepath), "Make sure to save the plot under saved_map.png!"
    image = Image.open(filepath)
    prompt = (
        f"Here is a user-given task and the agent steps: {agent_memory.get_succinct_steps()}. Now here is the plot that was made."
        "Please check that the reasoning process and plot are correct: do they correctly answer the given task?"
        "First list reasons why yes/no, then write your final decision: PASS in caps lock if it is satisfactory, FAIL if it is not."
        "Don't be harsh: if the plot mostly solves the task, it should pass."
        "To pass, a plot should be made using px.scatter_map and not any other method (scatter_map looks nicer)."
    )
    messages = [
        {
            "role": "user",
            "content": [
                {
                    "type": "text",
                    "text": prompt,
                },
                {
                    "type": "image_url",
                    "image_url": {"url": make_image_url(encode_image_base64(image))},
                },
            ],
        }
    ]
    output = multimodal_model(messages).content
    print("Feedback: ", output)
    if "FAIL" in output:
        raise Exception(output)
    return True


manager_agent = CodeAgent(
    model=HfApiModel("deepseek-ai/DeepSeek-R1", provider="together", max_tokens=8096),
    tools=[calculate_cargo_travel_time],
    managed_agents=[web_agent],
    additional_authorized_imports=[
        "geopandas",
        "plotly",
        "shapely",
        "json",
        "pandas",
        "numpy",
    ],
    planning_interval=5,
    verbosity_level=2,
    final_answer_checks=[check_reasoning_and_plot],
    max_steps=15,
)
```
**Output**
```
CodeAgent | deepseek-ai/DeepSeek-R1
├── ✅ Authorized imports: ['geopandas', 'plotly', 'shapely', 'json', 'pandas', 'numpy']
├── 🛠️ Tools:
│   ┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
│   ┃ Name                        ┃ Description                           ┃ Arguments                             ┃
│   ┡━━━━━━━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┩
│   │ calculate_cargo_travel_time │ Calculate the travel time for a cargo │ origin_coords (`array`): Tuple of     │
│   │                             │ plane between two points on Earth     │ (latitude, longitude) for the         │
│   │                             │ using great-circle distance.          │ starting point                        │
│   │                             │                                       │ destination_coords (`array`): Tuple   │
│   │                             │                                       │ of (latitude, longitude) for the      │
│   │                             │                                       │ destination                           │
│   │                             │                                       │ cruising_speed_kmh (`number`):        │
│   │                             │                                       │ Optional cruising speed in km/h       │
│   │                             │                                       │ (defaults to 750 km/h for typical     │
│   │                             │                                       │ cargo planes)                         │
│   │ final_answer                │ Provides a final answer to the given  │ answer (`any`): The final answer to   │
│   │                             │ problem.                              │ the problem                           │
│   └─────────────────────────────┴───────────────────────────────────────┴───────────────────────────────────────┘
└── 🤖 Managed agents:
    └── web_agent | CodeAgent | Qwen/Qwen2.5-Coder-32B-Instruct
        ├── ✅ Authorized imports: []
        ├── 📝 Description: Browses the web to find information
        └── 🛠️ Tools:
            ┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
            ┃ Name                        ┃ Description                       ┃ Arguments                         ┃
            ┡━━━━━━━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┩
            │ web_search                  │ Performs a google web search for  │ query (`string`): The search      │
            │                             │ your query then returns a string  │ query to perform.                 │
            │                             │ of the top search results.        │ filter_year (`integer`):          │
            │                             │                                   │ Optionally restrict results to a  │
            │                             │                                   │ certain year                      │
            │ visit_webpage               │ Visits a webpage at the given url │ url (`string`): The url of the    │
            │                             │ and reads its content as a        │ webpage to visit.                 │
            │                             │ markdown string. Use this to      │                                   │
            │                             │ browse webpages.                  │                                   │
            │ calculate_cargo_travel_time │ Calculate the travel time for a   │ origin_coords (`array`): Tuple of │
            │                             │ cargo plane between two points on │ (latitude, longitude) for the     │
            │                             │ Earth using great-circle          │ starting point                    │
            │                             │ distance.                         │ destination_coords (`array`):     │
            │                             │                                   │ Tuple of (latitude, longitude)    │
            │                             │                                   │ for the destination               │
            │                             │                                   │ cruising_speed_kmh (`number`):    │
            │                             │                                   │ Optional cruising speed in km/h   │
            │                             │                                   │ (defaults to 750 km/h for typical │
            │                             │                                   │ cargo planes)                     │
            │ final_answer                │ Provides a final answer to the    │ answer (`any`): The final answer  │
            │                             │ given problem.                    │ to the problem                    │
            └─────────────────────────────┴───────────────────────────────────┴───────────────────────────────────┘
```

---

## ✅ Key Takeaways

- MAS helps split tasks across focused, memory-efficient agents
- Tools + planning + delegation = scalable, smart systems
- Visual output enhances interpretability
- `smolagents` supports agent orchestration, web tools, and multimodal reasoning


## 🔗 Resources

- [Multi-Agent Systems](https://huggingface.co/docs/smolagents/main/en/examples/multiagents) – Overview of multi-agent systems.
- [What is Agentic RAG?](https://weaviate.io/blog/what-is-agentic-rag) – Introduction to Agentic RAG.
- [Multi-Agent RAG System 🤖🤝🤖 Recipe](https://huggingface.co/learn/cookbook/multiagent_rag_system) – Step-by-step guide to building a multi-agent RAG system.