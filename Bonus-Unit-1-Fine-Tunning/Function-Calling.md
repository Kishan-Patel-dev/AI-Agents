# 🔧 What Is Function Calling?
Function calling allows an LLM to **interact with the outside world** by:
- Deciding what action (function) to take.
- Providing the right inputs (parameters).
- Waiting for the result.
- Continuing the conversation based on the result.

It's **less about prompting** and more about **training** or fine-tuning the model to:
1. Understand when to use a function.
2. Format the call properly.
3. Stop generating while waiting for tool output.

---

## 🧠 How Does It Work?
The workflow of an agent using function-calling typically looks like this:

1. **Think** – Figure out what needs to be done.
2. **Act** – Call a function (tool) using a special format.
3. **Observe** – Wait for the environment/tool to return a result.
4. **Continue** – Use the result in further responses.

---

## 🧩 Chat Template Enhancements
To support function-calling, models (like those served via APIs) often use *special roles or tokens*:
- `role: "assistant"` with a `function_call` field initiates an action.
- `role: "tool"` is used to send back the result.
- Special tokens like `[TOOL_CALLS]` and `[TOOL_RESULTS]` help models handle tool interactions explicitly in chat formats.

---

## 🧪 Why Fine-Tuning Is Needed
In Unit 1, agents just received a list of tools and relied on inference to use them.
With **function-calling**, you're **fine-tuning** the model to **internalize tool usage**—making it act more like a "trained API client".

To enable this in models like [`google/gemma-2-2b-it`](https://huggingface.co/google/gemma-2-2b-it), you:
- Add new **special tokens**.
- Use **fine-tuning or LoRA** to train the model on calling functions correctly.

---

