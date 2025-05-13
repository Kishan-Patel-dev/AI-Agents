# 🛠️ Building and Integrating Tools for Your Agent

> Equip Alfred with modern knowledge capabilities: live web search, weather awareness, and Hugging Face Hub statistics—ensuring he’s the most insightful and dynamic host at the gala.

---

## 🌐 Web Search Tool: DuckDuckGo

To stay updated with global events, Alfred uses DuckDuckGo to answer real-time questions:

```python
from llama_index.tools.duckduckgo import DuckDuckGoSearchToolSpec
from llama_index.core.tools import FunctionTool

# Initialize the DuckDuckGo search tool
tool_spec = DuckDuckGoSearchToolSpec()

search_tool = FunctionTool.from_defaults(tool_spec.duckduckgo_full_search)
# Example usage
response = search_tool("Who's the current President of France?")
print(response.raw_output[-1]['body'])
```

🔍 **Expected Output**:
The President of the French Republic is the head of state of France. The current President is Emmanuel Macron since 14 May 2017 defeating Marine Le Pen in the second round of the presidential election on 7 May 2017. List of French presidents (Fifth Republic) N° Portrait Name ...

---

## ☁️ Weather Tool: Dummy API (or OpenWeatherMap)

Ensure ideal fireworks timing with real-time weather insights:

```python
import random
from llama_index.core.tools import FunctionTool

def get_weather_info(location: str) -> str:
    """Fetches dummy weather information for a given location."""
    # Dummy weather data
    weather_conditions = [
        {"condition": "Rainy", "temp_c": 15},
        {"condition": "Clear", "temp_c": 25},
        {"condition": "Windy", "temp_c": 20}
    ]
    # Randomly select a weather condition
    data = random.choice(weather_conditions)
    return f"Weather in {location}: {data['condition']}, {data['temp_c']}°C"

# Initialize the tool
weather_info_tool = FunctionTool.from_defaults(get_weather_info)
```

🌤️ **Use case**: Alfred checks if the sky is clear for fireworks.

---

## 🤖 Hugging Face Hub Stats Tool

Let Alfred impress top AI builders by recognizing their most downloaded models:

```python
import random
from llama_index.core.tools import FunctionTool
from huggingface_hub import list_models

def get_hub_stats(author: str) -> str:
    """Fetches the most downloaded model from a specific author on the Hugging Face Hub."""
    try:
        # List models from the specified author, sorted by downloads
        models = list(list_models(author=author, sort="downloads", direction=-1, limit=1))

        if models:
            model = models[0]
            return f"The most downloaded model by {author} is {model.id} with {model.downloads:,} downloads."
        else:
            return f"No models found for author {author}."
    except Exception as e:
        return f"Error fetching models for {author}: {str(e)}"

# Initialize the tool
hub_stats_tool = FunctionTool.from_defaults(get_hub_stats)

# Example usage
print(hub_stats_tool("facebook")) # Example: Get the most downloaded model by Facebook
```

🔥 **Example**:
`hub_stats_tool("facebook")`
➡️ Alfred: *“The most downloaded model by Facebook is `facebook/esmfold_v1` with 13 million+ downloads.”*

---

## 🧠 Integrating Tools with Alfred

Combine all tools into a single powerful agent using `AgentWorkflow`:

```python
from llama_index.core.agent.workflow import AgentWorkflow
from llama_index.llms.huggingface_api import HuggingFaceInferenceAPI

# Initialize the Hugging Face model
llm = HuggingFaceInferenceAPI(model_name="Qwen/Qwen2.5-Coder-32B-Instruct")
# Create Alfred with all the tools
alfred = AgentWorkflow.from_tools_or_functions(
    [search_tool, weather_info_tool, hub_stats_tool],
    llm=llm
)

# Example query Alfred might receive during the gala
response = await alfred.run("What is Facebook and what's their most popular model?")

print("🎩 Alfred's Response:")
print(response)
```

🧠 **Alfred’s Response**: Combines LLM knowledge + real-time hub data.
```
🎩 Alfred's Response:
Facebook is a social networking service and technology company based in Menlo Park, California. It was founded by Mark Zuckerberg and allows people to create profiles, connect with friends and family, share photos and videos, and join groups based on shared interests. The most popular model by Facebook on the Hugging Face Hub is `facebook/esmfold_v1` with 13,109,861 downloads.
```

---

## 📌 Conclusion

Alfred can now:

* Conduct live web searches
* Check and discuss weather conditions
* Share AI model download stats from Hugging Face

> 🔧 Challenge: Build a tool that fetches the latest **news on a specific topic** (e.g., “AI advancements”) using your preferred news API.
