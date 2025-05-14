# 🛠️ Hands-On: Submit Your Final Agent!


You're now ready to **put your agent to the test** and try out the **GAIA benchmark** in a real-world evaluation. Let's walk through how to **submit your agent** and get your score on the **student leaderboard**.

## 🚀 Mission Accomplished: AI Agent Built & Dominated! 💯

I'm thrilled to share that I've **successfully built an AI Agent** and achieved a **score of over 90%** on the GAIA benchmark! 🧠✨

🔗 **Check out the full project here:**
👉 [**AI Agent – RobotPai**](https://huggingface.co/spaces/kishan-patel-dev/RobotPai/tree/main)

This agent combines reasoning, retrieval, tool usage, and real-world understanding—all wrapped up in one powerful system. 🎩🤖

> Built for excellence. Tested with GAIA. Ready for the future. 🌍🔥


---

## 📚 The Dataset

* ✅ A curated set of **20 Level 1 questions** from GAIA’s validation set.
* 🧪 Chosen for requiring **minimal tools** and **fewer reasoning steps**.
* 🎯 Goal: **Score at least 30%** for successful course completion.

> This is a fair and achievable challenge based on the current performance of leading AI systems.

---

## 🔄 The Submission Process

You’ll interact with a **custom API** to evaluate your agent. Here are the key endpoints:
![API Documentation](https://agents-course-unit4-scoring.hf.space/docs)

| Endpoint               | Description                                             |
| ---------------------- | ------------------------------------------------------- |
| `GET /questions`       | Get the full list of evaluation questions               |
| `GET /random-question` | Fetch a random question                                 |
| `GET /files/{task_id}` | Download any necessary files for a task                 |
| `POST /submit`         | Submit your answers for scoring and leaderboard ranking |

### ❗ Submission is scored using **EXACT MATCH**—so [prompt](https://huggingface.co/spaces/gaia-benchmark/leaderboard) your agent carefully.

---

## 🧑‍💻 What You’ll Submit

To submit, you must provide:

1. **`username`** – Your Hugging Face username
2. **`agent_code`** – Link to your **public Hugging Face Space code** (e.g., `https://huggingface.co/spaces/your-username/your-agent/tree/main`)
3. **`answers`** – A list of responses in the format:

```json
[
  {"task_id": "abc123", "submitted_answer": "apple, banana"},
  ...
]
```

> Your space **must be public** if you want to appear on the leaderboard.

---

## 🎨 Use the Template, Then Customize

A basic **submission template** is provided to help you get started. You’re encouraged to:

* Restructure or improve the code
* Integrate your own tools or logic
* Showcase your own agent design

This is your chance to get creative! 🧠✨

---

## 🏆 Join the Leaderboard!

Track your progress and compare with your peers on the [📈 GAIA Student Leaderboard](https://huggingface.co/spaces/learn-gaia/leaderboard).

> 📣 **Reminder:** This leaderboard is just for fun and learning. If high scores are submitted without code backing, they may be removed.

---

## 🚀 Let’s Go!

* Start by duplicating the template on Hugging Face.
* Keep your space public.
* Score 30%+ to **earn your certificate** and join the leaderboard!

Good luck, agent architect! 🧑‍🎓🤖

---
