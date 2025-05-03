#  Building Agents That Use Code 

---

### 🔧 **What Are Code Agents in smolagents?**
**Code agents** are the default and most powerful agent type in the `smolagents` library. They **generate and execute Python tools code** to perform tasks instead of using traditional JSON-based tool calls.

![Code Agents](../../assets/code-vs-json-actions.png)
---

### ✅ **Why Use Code Agents?**
From the paper *Executable Code Actions Elicit Better LLM Agents*, and smolagents' own principles, code-based actions have several advantages:
- **Composability**: Code can be reused and combined easily.
- **Object management**: Directly handle complex data types like images or objects.
- **Expressiveness**: Code can represent any logic or computation.
- **LLM-native**: LLMs are trained on vast code data and generate better Python than structured JSON.

---

### 🔁 **How Does a CodeAgent Work?**
A `CodeAgent` is a special kind of `MultiStepAgent` that uses a loop to reason, generate, and execute Python code:

![Code Agent Working](../../assets/codeagent_docs.png)

A `CodeAgent` performs actions through a cycle of steps, with existing variables and knowledge being incorporated into the agent’s context, which is kept in an execution log:

1. The system prompt is stored in a `SystemPromptStep`, and the user query is logged in a `TaskStep`.

2. Then, the following while loop is executed:

    2.1. Method `agent.write_memory_to_messages() `writes the agent’s logs into a list of LLM-readable chat messages.

    2.2. These messages are sent to a `Model`, which generates a completion.

    2.3. The completion is parsed to extract the action, which, in our case, should be a code snippet since we’re working with a `CodeAgent`.

    2.4. The action is executed.

    2.5. The results are logged into memory in an `ActionStep`.

At the end of each step, if the agent includes any function calls (in `agent.step_callback`), they are executed.

---

### 🧪 **Example Use Cases**

Try out the Example in [Google Colab](https://huggingface.co/agents-course/notebooks/blob/main/unit2/smolagents/code_agents.ipynb) or use the [Code Agent](./code_agents.ipynb)

**0. Install Framework `smolagents` and Login with Hagging Face to access Serverless Inference API**
```python
pip install smolagents -U

from huggingface_hub import login
login()
```

**1. Playlist Selection**  
Use a DuckDuckGo search tool to find music recommendations for a party:
```python
from smolagents import CodeAgent, DuckDuckGoSearchTool, HfApiModel
agent = CodeAgent(tools=[DuckDuckGoSearchTool()], model=HfApiModel())
agent.run("Find the best playlist for a Batman-themed party.")
```

**2. Custom Tool: Menu Suggestion**
Define your own Python custome function act as tool using the `@tool` decorator:
```python
from smolagents import CodeAgent, tool, HfApiModel

# Tool to suggest a menu based on the occasion
@tool
def suggest_menu(occasion: str) -> str:
    """
    Suggests a menu based on the occasion.
    Args:
        occasion (str): The type of occasion for the party. Allowed values are:
                        - "casual": Menu for casual party.
                        - "formal": Menu for formal party.
                        - "superhero": Menu for superhero party.
                        - "custom": Custom menu.
    """
    if occasion == "casual":
        return "Pizza, snacks, and drinks."
    elif occasion == "formal":
        return "3-course dinner with wine and dessert."
    elif occasion == "superhero":
        return "Buffet with high-energy and healthy food."
    else:
        return "Custom menu for the butler."

# Alfred, the butler, preparing the menu for the party
agent = CodeAgent(tools=[suggest_menu], model=HfApiModel())

# Preparing the menu for the party
agent.run("Prepare a formal menu for the party.")
```

**3. Importing Modules for Tasks**
`smolagents` specializes in agents that write and execute Python code snippets, offering sandboxed execution for security.

**Code execution has strict security measures** - imports outside a predefined safe list are blocked by default. However, you can authorize additional imports by passing them as strings in `additional_authorized_imports`.

Allow imports like `datetime` to calculate when party preparations will finish:
```python
from smolagents import CodeAgent, HfApiModel
import numpy as np
import time
import datetime

agent = CodeAgent(tools=[], model=HfApiModel(), additional_authorized_imports=['datetime'])

agent.run(
    """
    Alfred needs to prepare for the party. Here are the tasks:
    1. Prepare the drinks - 30 minutes
    2. Decorate the mansion - 60 minutes
    3. Set up the menu - 45 minutes
    4. Prepare the music and playlist - 45 minutes

    If we start right now, at what time will the party be ready?
    """
)
```
### Summary
In summary, `smolagents` specializes in agents that write and execute **Python code snippets**, offering sandboxed execution for security.

---

### 🌐 **Sharing Agents on Hugging Face**
Agents can be pushed to or loaded from the Hugging Face Hub:
```python
agent.push_to_hub("Kishan-patel-dev/AlfredAgent")

# Download & Use
alfred_agent = agent.from_hub('Kishan-patel-dev/AlfredAgent', trust_remote_code=True)

alfred_agent.run("Give me the best playlist for a party at Wayne's mansion. The party idea is a 'villain masquerade' theme")  
```

---

### 🧠 In Summary
- `smolagents` makes it easy to build **LLM agents that write and run Python**.
- Code-based reasoning is **more powerful** than JSON-based tool use.
- You can build tools, import safely, use web search, and share agents with the community.
- **Learn how we can create Tool Calling Agents**, the second type of agent available in `smolagents`.

---

## 🔗 Resources
- [smolagents Blog](https://huggingface.co/blog/smolagents) - Introduction to smolagents and code interactions
- [smolagents: Building Good Agents](https://huggingface.co/docs/smolagents/tutorials/building_good_agents) - Best practices for reliable agents
- [Building Effective Agents - Anthropic](https://www.anthropic.com/research/building-effective-agents) - Agent design principles
- [Sharing runs with OpenTelemetry](https://huggingface.co/docs/smolagents/tutorials/inspect_runs) - Details about how to setup OpenTelemetry for tracking your agents.