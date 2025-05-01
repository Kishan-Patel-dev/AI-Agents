
# 🔥 Fine-Tune LLM Model for Function-Calling

## ❓ How do we train our model for function-calling?
<details>
<summary>Click to expand the answer</summary>

We train the model in **three steps**:

1. **Pretraining** — Base model learns to predict the next token.
2. **Instruction-tuning** — Fine-tunes the model to follow human instructions.
3. **Alignment** — Tailors the model for specific behaviors (e.g., politeness in customer service).

➡️ We start with an **instruction-tuned model** like `google/gemma-2-2b-it` for minimal training.

</details>

---

## 🔧 Steps to Fine-Tune Your Model

### 1️⃣ Select a Base Model
> 🎯 **Use this model:** [`google/gemma-2-2b-it`](https://huggingface.co/google/gemma-2-2b-it)

- It's already tuned for instruction-following.
- Saves time and compute over starting from scratch.

---

### 2️⃣ Prepare Your Dataset

✅ Your dataset should teach this flow:  
**Thought → Act → Observe**

```yaml
- user: "What's the weather in Paris?"
  model: 
    function_call:
      name: getWeather
      arguments:
        city: "Paris"
```

<details>
<summary>✅ Include examples like...</summary>

- When to call a function
- How to generate correct parameters
- How to handle function outputs

</details>

---

### 3️⃣ Use LoRA for Efficient Fine-Tuning

> 💡 **What is LoRA?**  
>Low-Rank Adaptation for Large Language Models
> A lightweight training technique that significantly *reduces the number of trainable parameters.*
>Works by **inserting a smaller number of new weights as an adapter into the model to train.**
>LoRA is particularly useful for adapting **large language models** to specific tasks or domains while keeping resource requirements manageable. This helps reduce the *memory required to train a model.*

![LoRA](../assets/LoRA.gif)

**Benefits of LoRA:**
- 🧠 Train fewer parameters
- 🚀 Faster training
- 💾 Smaller model files

<details>
<summary>📘 How it works</summary>

- Freeze the base model
- Add small adapter layers (LoRA modules)
- Train only the adapters

</details>

---

### 4️⃣ Fine-Tune with Hugging Face + PEFT

```bash
# Install dependencies
pip install peft transformers accelerate

# Example fine-tuning script (pseudo)
from peft import get_peft_model, LoraConfig
from transformers import AutoModelForCausalLM

model = AutoModelForCausalLM.from_pretrained("google/gemma-2-2b-it")
peft_model = get_peft_model(model, LoraConfig(...))
peft_model.train(...)
```

[👉 Open Notebook in Colab](https://huggingface.co/agents-course/notebooks/blob/main/bonus-unit1/bonus-unit1.ipynb)
---

### 5️⃣ Evaluate and I mastered understanding function-calling and how to fine-tune your model to do function-calling!terate

✅ **Evaluation Criteria:**

- 🎯 Is the function called?
- 🧮 Are the parameters correct?
- 📦 Is the response handled well?

```markdown
- [ ] Check accuracy of generated calls
- [ ] Review incorrect parameter values
- [ ] Adjust dataset based on failure modes
```

---

### Conclusion

Mastered understanding function-calling and how to fine-tune your model to do function-calling! by try to fine-tune different models. The best way to learn is by trying.

## 📚 Additional Resources

- [LoRN Working](https://huggingface.co/learn/llm-course/chapter11/4?fw=pt)
- ![LoRA Video](https://img.youtube.com/vi/DhRoTONcyZE/0.jpg)](https://www.youtube.com/watch?v=DhRoTONcyZE)
- 🧠 [Fine-Tuning LLaMA 3 for Function Calling (Medium)](https://gautam75.medium.com/fine-tuning-llama-3-1-8b-for-function-calling-using-lora-159b9ee66060?utm_source=chatgpt.com)  
- 🛠 [Prompt Engineering Guide – Function Calling](https://www.promptingguide.ai/applications/function_calling?utm_source=chatgpt.com)

---

### ✅ Summary Checklist

```markdown
- [x] Choose instruction-tuned base model
- [x] Create function-calling dataset
- [x] Implement LoRA
- [x] Fine-tune with PEFT
- [x] Evaluate & iterate
```
```
