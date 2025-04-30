# 📚 Unit 1: What is an Agent?

![Unit 1:  Plan](./assets/unit-1-plan.jpg)
## 🌟 Big Picture: Alfred the Agent
- **Analogy**: Alfred receives a request (“I want coffee”), **reasons and plans** the steps needed, **uses tools** (coffee machine), and **executes** actions to fulfill the request.

![Agent Workflow](./assets/agent-workflow.jpg)
- **Key Concept**:  
  ➔ **An Agent = Reasoning + Planning + Acting** using available tools.

---

## 🔥 Formal Definition
> An **Agent** is a system that uses an **AI model** to **interact** with its environment to achieve a **user-defined objective**, combining **reasoning**, **planning**, and **action execution**.

Two main parts:
- 🧠 **Brain(AI Model)**: AI model that reasons and plans (usually an LLM).
- 🛠️ **Body**: Tools and capabilities the agent can use to act.

---

## 📈 Spectrum of "Agency"
| Agency Level | Description | Type of Agent | Example |
|:------------|:------------|:-------------|:--------|
| ☆☆☆ | No impact on flow | Simple Processor | `process_llm_output()` |
| ★☆☆ | Control flow choice | Router | `if llm_decision() then...` |
| ★★☆ | Select function to run | Tool Caller | `run_function(tool, args)` |
| ★★★ | Control loops / multi-steps | Multi-Step Agent | `while continue(): step()` |
| ★★★ | Trigger other agents | Multi-Agent | `if trigger: execute_agent()` |

---

## 🧠 What AI Models are used?
- The most common ***AI model found in Agents*** is an **LLM (Large Language Model)**, which takes **Text** as an input and outputs **Text** as well like GPT4 from OpenAI, LLama from Meta, Gemini from Google etc.
- Some Agents can use **VLMs** (Vision Language Models) to understand images too.

---

## 🛠️ How does an Agent act on its environment?
- LLMs **only output text**.
- **Tools** extend LLMs so that they can **take real actions** (e.g., sending an email, generating an image).
- An Agent can perform any task we implement via **Tools** to complete Actions.
- The **design of the Tools is very important and has a great impact on the quality of your Agent**.

**Example Tool:**
```python
def send_message_to(recipient, message):
    """Send an email message to a recipient."""
```
The LLM would output:
```python
send_message_to("Manager", "Can we postpone today's meeting?")
```
And the agent would execute it.

---

## 🛠️ Actions vs Tools
- **Tool**: A specific operation (e.g., send email, search web).
- **Action**: A full **task**, possibly combining several tools.

---

## 🌍 Examples of Agents
- **Virtual Assistants** (Siri, Alexa): handle tasks across apps/devices.
- **Customer Service Chatbots**: interact with customers and backend databases.
- **NPCs in Video Games**: dynamic behaviors driven by LLMs.

---

## ✨ To summarize
An Agent is a system that uses an AI Model(LLM) as its core reasoning engine:
- **Understands natural language** (interpret commands)
- **Reasons and plans** (chooses steps and tools)
- **Acts in its environment** (takes real-world actions)

---

✅ Now that you understand **what an Agent is**, the course will move into the **Agent’s Brain**: **LLMs**!

---