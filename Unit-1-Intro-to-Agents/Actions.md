# 🚀 Actions: Enabling the Agent to Engage with Its Environment

## 📝 Overview
Actions are the concrete steps an **AI agent takes to interact with its environment**. Whether it's browsing the web for information or controlling a physical device, each action is a deliberate operation executed by the agent.

## 🔄 Types of Agent Actions

### 1. JSON Agent
- Actions specified in JSON format
- Structured and machine-readable
- Easy to parse and process

### 2. Code Agent
- Generates executable code blocks
- Typically in high-level languages like Python
- Offers greater flexibility and expressiveness

### 3. Function-calling Agent
- Subcategory of JSON Agent
- Fine-tuned to generate new messages for each action
- Specialized for function invocation

## 🎯 Types of Actions

| Type of Action | Description |
|----------------|-------------|
| Information Gathering | Performing web searches, querying databases, or retrieving documents |
| Tool Usage | Making API calls, running calculations, and executing code |
| Environment Interaction | Manipulating digital interfaces or controlling physical devices |
| Communication | Engaging with users via chat or collaborating with other agents |

## 🔑 The Stop and Parse Approach

### Key Components
1. **Generation in a Structured Format**
   - Clear, predetermined format (JSON or code)
   - Machine-readable structure
   - Consistent output pattern

2. **Halting Further Generation**
   - Stops after action completion
   - Prevents extra output
   - Ensures precision

3. **Parsing the Output**
   - External parser reads formatted action
   - Determines which Tool to call
   - Extracts required parameters

### Example
```json
Thought: I need to check the current weather for New York.
Action: {
  "action": "get_weather",
  "action_input": {"location": "New York"}
}
```

## 💻 Code Agents

An alternative approach is using *Code Agents*. The idea is: **instead of outputting a simple JSON object**, a Code Agent generates an **executable code block—typically in a high-level language like Python.**

![Code Agents](../assets/code-vs-json-actions.png)

### Advantages
- **Expressiveness**: Natural representation of complex logic
- **Modularity**: Reusable functions and modules
- **Debuggability**: Clear syntax for error detection
- **Direct Integration**: Seamless connection with external libraries

### Example
```python
# Code Agent Example: Retrieve Weather Information
def get_weather(city):
    import requests
    api_url = f"https://api.weather.com/v1/location/{city}?apiKey=YOUR_API_KEY"
    response = requests.get(api_url)
    if response.status_code == 200:
        data = response.json()
        return data.get("weather", "No weather information available")
    else:
        return "Error: Unable to fetch weather data."

# Execute the function and prepare the final answer
result = get_weather("New York")
final_answer = f"The current weather in New York is: {result}"
print(final_answer)
```

## 🎯 Key Takeaways
1. Actions bridge internal reasoning and real-world interactions
2. Structured execution ensures precision
3. Stop and parse approach enables reliable processing
4. Different agent types offer various advantages
5. Code agents provide enhanced flexibility and expressiveness



---
*💡 Tip: When designing agent actions, always consider the balance between flexibility and structure. The stop and parse approach helps maintain this balance.*