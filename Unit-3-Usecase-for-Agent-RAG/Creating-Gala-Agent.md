# 🎩 Creating Your Gala Agent

> It's time to bring Alfred to life! Assemble all tools into a fully integrated agent capable of charming guests, sharing the latest updates, and orchestrating the perfect evening.

---

## 🧱 Assembling Alfred: The Complete Agent

Instead of redefining everything, we import our pre-built tools:

```python
# Import necessary libraries
from llama_index.core.agent.workflow import AgentWorkflow
from llama_index.llms.huggingface_api import HuggingFaceInferenceAPI

from tools import search_tool, weather_info_tool, hub_stats_tool
from retriever import guest_info_tool
```

🧠 Initialize Alfred with a powerful LLM and all tools:

```python
# Initialize the Hugging Face model
llm = HuggingFaceInferenceAPI(model_name="Qwen/Qwen2.5-Coder-32B-Instruct")

# Create Alfred with all the tools
alfred = AgentWorkflow.from_tools_or_functions(
    [guest_info_tool, search_tool, weather_info_tool, hub_stats_tool],
    llm=llm,
)
```

---

## 🧪 Using Alfred: End-to-End Examples

### 📘 Example 1: Finding Guest Info

```python
query = "Tell me about Lady Ada Lovelace. What's her background?"
response = await alfred.run(query)

print("🎩 Alfred's Response:")
print(response.response.blocks[0].text)
```

🎩 Alfred:
*Lady Ada Lovelace was an English mathematician...*

---

### 🌦️ Example 2: Checking the Weather

```python
query = "What's the weather like in Paris tonight? Will it be suitable for our fireworks display?"
response = await alfred.run(query)

print("🎩 Alfred's Response:")
print(response)
```

🎩 Alfred:
*The weather in Paris tonight is rainy with a temperature of 15°C.*

---

### 🧠 Example 3: Impressing AI Researchers

```python
query = "One of our guests is from Google. What can you tell me about their most popular model?"
response = await alfred.run(query)

print("🎩 Alfred's Response:")
print(response)
```

🎩 Alfred:
*The most popular model by Google is `google/electra-base-discriminator`, with over 28 million downloads.*

---

### 🤝 Example 4: Combining Multiple Tools

```python
query = "One of our guests is from Google. What can you tell me about their most popular model?"
response = await alfred.run(query)

print("🎩 Alfred's Response:")
print(response)
```

🎩 Alfred:
*🎩 Alfred's Response:
Here are some recent advancements in wireless energy that you might find useful for your conversation with Dr. Nikola Tesla:

1. **Advancements and Challenges in Wireless Power Transfer**: This article discusses the evolution of wireless power transfer (WPT) from conventional wired methods to modern applications, including solar space power stations. It highlights the initial focus on microwave technology and the current demand for WPT due to the rise of electric devices.

2. **Recent Advances in Wireless Energy Transfer Technologies for Body-Interfaced Electronics**: This article explores wireless energy transfer (WET) as a solution for powering body-interfaced electronics without the need for batteries or lead wires. It discusses the advantages and potential applications of WET in this context.

3. **Wireless Power Transfer and Energy Harvesting: Current Status and Future Trends**: This article provides an overview of recent advances in wireless power supply methods, including energy harvesting and wireless power transfer. It presents several promising applications and discusses future trends in the field.

4. **Wireless Power Transfer: Applications, Challenges, Barriers, and the*

---

## 🧠 Advanced Features: Memory & Context

Enable Alfred to remember the conversation flow using `Context`:

```python
from llama_index.core.workflow import Context

alfred = AgentWorkflow.from_tools_or_functions(
    [guest_info_tool, search_tool, weather_info_tool, hub_stats_tool],
    llm=llm
)

# Remembering state
ctx = Context(alfred)

# First interaction
response1 = await alfred.run("Tell me about Lady Ada Lovelace.", ctx=ctx)
print("🎩 Alfred's First Response:")
print(response1)

# Second interaction (referencing the first)
response2 = await alfred.run("What projects is she currently working on?", ctx=ctx)
print("🎩 Alfred's Second Response:")
print(response2)
```

🔄 **Why memory isn’t automatic?**
Each framework handles memory differently to maintain modularity and control:

* **smolagents**: No memory unless `reset=False`
* **LlamaIndex**: Memory passed via `Context`
* **LangGraph**: Memory via message retrievers or `MemorySaver`

---

## 🏁 Conclusion

🎉 Congratulations! Alfred is now fully equipped to:

* 🧑‍💼 Retrieve guest backgrounds
* 🌦️ Weather condition through API
* 📊 Share Hugging Face model stats
* 🔍 Search the web for live updates
* 💬 Maintain conversation flow with memory

> Alfred is ready to make your gala unforgettable—with elegance, intelligence, and a touch of technological magic.
