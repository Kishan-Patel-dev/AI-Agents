# 🕵️‍♂️ Vision Agents with `smolagents`

<a href="./vision_agents.ipynb" target="_blank">
  <img src="https://img.shields.io/badge/Notebook-Open%20.ipynb-blue?style=for-the-badge&logo=jupyter" alt="Notebook Button"/>
</a>

Smolagents now support **vision-language models (VLMs)** like GPT-4o, enabling agents to interpret images and make decisions based on visual inputs. This capability is crucial for real-world tasks such as **guest verification**, **document understanding**, and **web browsing**.

---

## 🧠 Use Case: Verifing User Identity 

Tasked with verifying the identities by providing the Data & Images

Agent that verifies their identity by searching for visual information about their appearance using a VLM.

### 🔍 Static Image Verification

- **Scenario**: Alfred has past guest images and receives a new image of a guest.
- **Workflow**:
  1. Load images from URLs (e.g., Joker photos).
  2. Use `CodeAgent` with GPT-4o to describe appearance and determine identity.
  3. Sample output confirms the guest is The Joker.

### 🛠️ Code Snippet
**Loading Image**
```python
from PIL import Image
import requests
from io import BytesIO

image_urls = [
    "https://upload.wikimedia.org/wikipedia/commons/e/e8/The_Joker_at_Wax_Museum_Plus.jpg", # Joker image
    "https://upload.wikimedia.org/wikipedia/en/9/98/Joker_%28DC_Comics_character%29.jpg" # Joker image
]

images = []
for url in image_urls:
    headers = {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.0.0 Safari/537.36" 
    }
    response = requests.get(url,headers=headers)
    image = Image.open(BytesIO(response.content)).convert("RGB")
    images.append(image)

```
**Agent**
```python
from smolagents import CodeAgent, OpenAIServerModel

model = OpenAIServerModel(model_id="gpt-4o")

# Instantiate the agent
agent = CodeAgent(
    tools=[],
    model=model,
    max_steps=20,
    verbosity_level=2
)

response = agent.run(
    """
    Describe the costume and makeup that the comic character in these photos is wearing and return the description.
    Tell me if the guest is The Joker or Wonder Woman.
    """,
    images=images
)
```

**Output**
```
    {
        'Costume and Makeup - First Image': (
            'Purple coat and a purple silk-like cravat or tie over a mustard-yellow shirt.',
            'White face paint with exaggerated features, dark eyebrows, blue eye makeup, red lips forming a wide smile.'
        ),
        'Costume and Makeup - Second Image': (
            'Dark suit with a flower on the lapel, holding a playing card.',
            'Pale skin, green hair, very red lips with an exaggerated grin.'
        ),
        'Character Identity': 'This character resembles known depictions of The Joker from comic book media.'
    }
```

---

## 🌐 Dynamic Image Retrieval via Browsing

When images aren’t available in advance, agents can **dynamically search the web** using browser automation.

- Images are dynamically added to the agent’s memory during execution.
- Use `MultiStepAgent` class which is an abstraction of the ReAct framework. 
- This class operates in a structured cycle where various variables and knowledge are logged at different stages:
- - **SystemPromptStep**: Stores the system prompt.
- - **TaskStep**: Logs the user query and any provided input.
- - **ActionStep**: Captures logs from the agent’s actions and results.


### 💻 Tools Used
- [`smolagents[all]`](https://pypi.org/project/smolagents/)
- `helium`, `selenium` for browser control
- Custom tools: `search_item_ctrl_f`, `go_back`, `close_popups`
- Screenshot function (`save_screenshot`) added as a `step_callback`

```python
pip install "smolagents[all]" helium selenium python-dotenv
```
**Tools for Browsing**
```python
@tool
def search_item_ctrl_f(text: str, nth_result: int = 1) -> str:
    """
    Searches for text on the current page via Ctrl + F and jumps to the nth occurrence.
    Args:
        text: The text to search for
        nth_result: Which occurrence to jump to (default: 1)
    """
    elements = driver.find_elements(By.XPATH, f"//*[contains(text(), '{text}')]")
    if nth_result > len(elements):
        raise Exception(f"Match n°{nth_result} not found (only {len(elements)} matches found)")
    result = f"Found {len(elements)} matches for '{text}'."
    elem = elements[nth_result - 1]
    driver.execute_script("arguments[0].scrollIntoView(true);", elem)
    result += f"Focused on element {nth_result} of {len(elements)}"
    return result


@tool
def go_back() -> None:
    """Goes back to previous page."""
    driver.back()


@tool
def close_popups() -> str:
    """
    Closes any visible modal or pop-up on the page. Use this to dismiss pop-up windows! This does not work on cookie consent banners.
    """
    webdriver.ActionChains(driver).send_keys(Keys.ESCAPE).perform()
```

**Storing Screenshorts for VLM agents**
```python
def save_screenshot(step_log: ActionStep, agent: CodeAgent) -> None:
    sleep(1.0)  # Let JavaScript animations happen before taking the screenshot
    driver = helium.get_driver()
    current_step = step_log.step_number
    if driver is not None:
        for step_logs in agent.logs:  # Remove previous screenshots from logs for lean processing
            if isinstance(step_log, ActionStep) and step_log.step_number <= current_step - 2:
                step_logs.observations_images = None
        png_bytes = driver.get_screenshot_as_png()
        image = Image.open(BytesIO(png_bytes))
        print(f"Captured a browser screenshot: {image.size} pixels")
        step_log.observations_images = [image.copy()]  # Create a copy to ensure it persists, important!

    # Update observations with current URL
    url_info = f"Current url: {driver.current_url}"
    step_log.observations = url_info if step_logs.observations is None else step_log.observations + "\n" + url_info
    return
```

### 🔁 Agent Lifecycle
- `SystemPromptStep`, `TaskStep`, `ActionStep`
- Screenshots saved during `ActionStep` for VLM analysis

### 🛠️ Agent Setup
```python
from smolagents import CodeAgent, OpenAIServerModel, DuckDuckGoSearchTool
model = OpenAIServerModel(model_id="gpt-4o")

agent = CodeAgent(
    tools=[DuckDuckGoSearchTool(), go_back, close_popups, search_item_ctrl_f],
    model=model,
    additional_authorized_imports=["helium"],
    step_callbacks=[save_screenshot],
    max_steps=20,
    verbosity_level=2,
)
```

### 🔍 Task Prompt
```text
agent.run("""
I am Alfred, the butler of Wayne Manor, responsible for verifying the identity of guests at party. A superhero has arrived at the entrance claiming to be Wonder Woman, but I need to confirm if she is who she says she is.

Please search for images of Wonder Woman and generate a detailed visual description based on those images. Additionally, navigate to Wikipedia to gather key details about her appearance. With this information, I can determine whether to grant her access to the event.
""" + helium_instructions)
```

### ✅ Final Result
> *Final answer: Wonder Woman is typically depicted wearing a red and gold bustier, blue shorts or skirt with white stars, a golden tiara, silver bracelets, and a golden Lasso of Truth. She is Princess Diana of Themyscira, known as Diana Prince in the world of men.*

---

### Notebook Video
![Vision-browser Agent example using smolagent](https://youtu.be/rObJel7-OLc)

## 📚 Further Reading
- [We just gave sight to smolagents](https://huggingface.co/blog/smolagents-can-see) - Blog describing the vision agent functionality.
- [Web Browser Automation with Agents 🤖🌐](https://huggingface.co/docs/smolagents/examples/web_browser) - Example for Web browsing using a vision agent.
- [Web Browser Vision Agent Example](https://github.com/huggingface/smolagents/blob/main/src/smolagents/vision_web_browser.py) - Example for Web browsing using a vision agent.

### Code Quiz 
[Smolagents Code Quiz](https://huggingface.co/spaces/agents-course/unit2_smolagents_quiz)