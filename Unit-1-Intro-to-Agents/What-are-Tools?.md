# 📚 What Are Tools?
**Tools** are external **functions** given to an LLM (Large Language Model) to extend what it can do — such as fetching real-time information, performing calculations, generating images, or interacting with APIs.

---

# 🔧 Examples of Common Tools
| Tool Type       | Description |
|-----------------|-------------|
| Web Search      | Fetches updated info from the internet |
| Image Generation| Creates images from text |
| Retrieval       | Pulls data from external databases |
| API Interface   | Talks to external services (GitHub, Spotify, etc.) |

---

# 🔥 Why Use Tools?
- **LLMs alone are limited**: They can't fetch live data or perform reliable math on their own.
- **Tools** let agents access external actions/data reliably and safely.
- **Good Tool = Clear Objective**: Should supplement the LLM’s limitations (e.g., calculator, web search).

---

# 🛠️ Components of a Tool
A Tool should have:
- A **clear name**.
- A **textual description**.
- **Arguments** (input values and types).
- (Optional) **Outputs** (output type).

**Example:**
```python
def calculator(a: int, b: int) -> int:
    """Multiply two integers."""
    return a * b
```
Text description:
```
Tool Name: calculator, Description: Multiply two integers., Arguments: a: int, b: int, Outputs: int
```

---

# 📤 How Tools Work in an Agent
- The LLM **generates a tool call as text** like: `call weather_tool('Paris')`.
- The **Agent interprets** this, **executes** the function in the background, and **returns results** to the LLM.
- **User never sees** the tool call process — only the final, polished output.

---

# 📝 Giving Tools to an LLM
- You inject tool descriptions into the **System Prompt**.
- The description must be **precise and consistent** (example: plain text, JSON, or even Python docstring format).

![System Prompt](../assets/Agent_system_prompt.png)

---

# ⚡ Automating Tool Descriptions (Decorator Approach)
- Use a `Tool` class and a Python `@tool` decorator to automatically extract:
  - Name
  - Description (docstring)
  - Input types
  - Output type

**Simplified view:**
```python
class Tool:
    def __init__(self, name, description, func, arguments, outputs):
        ...

    def to_string(self):
        ...
```

Usage:
```python
@tool
def calculator(a: int, b: int) -> int:
    """Multiply two integers."""
    return a * b
```
Calling `calculator.to_string()` gives you the perfect description for the System Prompt!

---
## 🛠️ Example: Agent Tool system for IDEs like VS Code
```
import os
import ast
import inspect

class CodeModificationTool:
    """
    A Tool class that gives the agent the capability to modify code files.
    
    Attributes:
        name (str): Name of the tool.
        description (str): A description of the tool.
        filepath (str): Path to the code file that the tool will modify.
    """
    def __init__(self, name: str, description: str, filepath: str):
        self.name = name
        self.description = description
        self.filepath = filepath

    def to_string(self) -> str:
        """
        Return a string representation of the tool.
        """
        return (
            f"Tool Name: {self.name}, "
            f"Description: {self.description}, "
            f"Filepath: {self.filepath}"
        )

    def read_code(self) -> str:
        """
        Read the current code from the file.
        """
        if not os.path.exists(self.filepath):
            raise FileNotFoundError(f"The file at {self.filepath} does not exist.")
        
        with open(self.filepath, 'r') as file:
            return file.read()

    def write_code(self, code: str) -> None:
        """
        Write modified code back to the file.
        """
        with open(self.filepath, 'w') as file:
            file.write(code)

    def add_function(self, function_name: str, function_code: str) -> None:
        """
        Add a function to the code file.
        """
        current_code = self.read_code()

        # Simple check to avoid duplication of functions
        if f"def {function_name}(" in current_code:
            print(f"Function {function_name} already exists in the code.")
            return

        # Adding the new function at the end of the file
        new_code = current_code + "\n\n" + function_code
        self.write_code(new_code)
        print(f"Function {function_name} has been added.")

    def modify_function(self, function_name: str, new_code: str) -> None:
        """
        Modify an existing function in the code file.
        """
        current_code = self.read_code()

        # Check if the function exists in the code
        if f"def {function_name}(" not in current_code:
            print(f"Function {function_name} does not exist in the code.")
            return

        # Use AST parsing to identify the function's location and replace it
        tree = ast.parse(current_code)
        for node in ast.walk(tree):
            if isinstance(node, ast.FunctionDef) and node.name == function_name:
                start = node.lineno - 1
                end = start + len(inspect.getsource(node).splitlines())

                # Replace the old function code with the new one
                lines = current_code.splitlines()
                lines[start:end] = new_code.splitlines()

                new_code = "\n".join(lines)
                self.write_code(new_code)
                print(f"Function {function_name} has been modified.")
                return

        print(f"Function {function_name} not found.")

    def delete_function(self, function_name: str) -> None:
        """
        Delete a function from the code file.
        """
        current_code = self.read_code()

        # Check if the function exists in the code
        if f"def {function_name}(" not in current_code:
            print(f"Function {function_name} does not exist in the code.")
            return

        # Use AST parsing to identify the function's location and remove it
        tree = ast.parse(current_code)
        for node in ast.walk(tree):
            if isinstance(node, ast.FunctionDef) and node.name == function_name:
                start = node.lineno - 1
                end = start + len(inspect.getsource(node).splitlines())

                # Remove the function code from the lines
                lines = current_code.splitlines()
                del lines[start:end]

                new_code = "\n".join(lines)
                self.write_code(new_code)
                print(f"Function {function_name} has been deleted.")
                return

        print(f"Function {function_name} not found.")


# Example Usage
tool = CodeModificationTool(name="CodeModifier", description="Modifies Python code files.", filepath="sample_code.py")

# Display the tool's description
print(tool.to_string())

# Add a new function to the code
function_code = """
def greet(name):
    print(f"Hello, {name}!")
"""
tool.add_function("greet", function_code)

# Modify an existing function (replace the function code)
new_code = """
def greet(name):
    print(f"Greetings, {name}!")
"""
tool.modify_function("greet", new_code)

# Delete an existing function
tool.delete_function("greet")

```
---

# 🌐 MCP (Model Context Protocol)
**Model Context Protocol (MCP)** is an **open protocol** that standardizes how applications provide tools to LLMs. MCP provides:
- A growing list of pre-built integrations that your LLM can directly plug into
- The flexibility to switch between LLM providers and vendors
- Best practices for securing your data within your infrastructure
This means that **any framework implementing MCP can leverage tools defined within the protocol**, eliminating the need to reimplement the same tool interface for each framework.
---

# 🚀 Key Takeaways
- **Tools = Extensions for LLMs(Functions that give LLMs extra Capabilities)**.
- **Precise Descriptions** are critical for tool usage.
- **Agents execute tool calls invisibly** from the user.
- **Auto-extract tool descriptions** using decorators for better scalability.
- **MCP helps** standardize tool usage across platforms.

---