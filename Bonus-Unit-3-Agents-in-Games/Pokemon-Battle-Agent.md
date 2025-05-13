# 🛠️ Build Your Own Pokémon Battle Agent

Now that you’ve seen what Agentic AI can do, it’s your turn to build a **Pokémon-style battle agent** using LLMs and Python. Your agent will compete in **turn-based battles** using a real Pokémon battle simulator.

You’ll be working with four main components:

---

## 🧠 Poke-env

**Poke-env** is a Python library originally built for reinforcement learning bots.
We’ve repurposed it to let LLMs interact with the battle environment.

![Pokemon](../assets/rl-gif.gif)

* Acts as a wrapper around **Pokémon Showdown**
* Your custom Agent class will inherit from `poke_env.player.Player`
* Provides API access to the game state

📄 [Documentation](https://poke-env.readthedocs.io/en/stable/)
💻 [GitHub Repository](https://github.com/hsahovic/poke-env)

---

## ⚔️ Pokémon Showdown

**Pokémon Showdown** is a fully featured, open-source battle simulator.

* Simulates real Pokémon battles
* Your agent plays **turn-by-turn**, just like a human player
* A custom server is deployed for this challenge

💻 [Repository](https://github.com/smogon/Pokemon-Showdown)
🌐 [Website](https://pokemonshowdown.com)

---

## 🔌 LLMAgentBase

A custom Python class that bridges **Poke-env** and your **LLM**.

### Core Features:

* Inherits from `Player`
* Formats battle state to send to the LLM
* Parses LLM responses to execute moves or switches

### Key Tools:

```python
STANDARD_TOOL_SCHEMA = {
    "choose_move": { ... },
    "choose_switch": { ... },
}
```

### Decision Logic:

* `choose_move(battle)`: Main method called each turn
* `_get_llm_decision(battle_state)`: Sends formatted state to LLM and returns decision
* `_format_battle_state()`: Builds a string of game context
* `_find_move_by_name()` / `_find_pokemon_by_name()`: Validates LLM-selected actions

You’ll **override `_get_llm_decision`** to implement your agent’s unique strategy.

🧠 See full implementation in `agents.py`

[Full source code: agent.py](https://huggingface.co/spaces/Jofthomas/twitch_streaming/blob/main/agents.py)
---

## 🧪 TemplateAgent

The `TemplateAgent` class extends `LLMAgentBase`. You’ll build your own agent by:

* Providing your API key
* Defining how to query your LLM
* Handling and parsing its response

### Template Snippet:

```python
class TemplateAgent(LLMAgentBase):
    def __init__(self, api_key, model="model-name", *args, **kwargs):
        ...
    
    async def _get_llm_decision(self, battle_state: str) -> Dict[str, Any]:
        ...
        return {"decision": {"name": function_name, "arguments": arguments}}
```

🧪 Prebuilt examples are included for **OpenAI**, **Mistral**, and **Gemini** models.
[Complete Examples](https://huggingface.co/spaces/Jofthomas/twitch_streaming/blob/main/agents.py)

---

## 🎮 It’s Battle Time!

With all the tools in place, you’re ready to:

1. Implement your custom decision logic.
2. Deploy your agent to our server.
3. Battle against others in real time.

Let’s see who can build the best Pokémon battle AI! 🔥