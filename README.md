# hunaizaasif-Personalization-Chatbot-with-Chainlit-made-document-by-sir-wase

# Project Idea: **Personalization Chatbot with Chainlit**

A friendly, conversational web-based agent that remembers user details across sessions, built with Gemini CLI and MCP servers.

---

## 📋 **Project Blueprint**

### **Step 1: Prerequisites**

### **Install Node.js and Gemini CLI**

- Install Node.js from [nodejs.org](http://nodejs.org)
- Run `npm install -g @google/gemini-cli`
- Test MCP Commands: `/mcp list`, `/mcp refresh`

### **MCP (Model Context Protocol) Introduction**

MCP is a protocol that allows AI models to connect to external data sources and tools. In the context of Gemini CLI, MCP servers act as bridges between your AI agent and various services like databases, APIs, or documentation repositories. This enables your chatbot to access real-time information and perform actions beyond its base knowledge.

**Key Benefits:**

- Access external knowledge bases dynamically
- Connect to multiple data sources simultaneously
- Execute tools and functions through standardized protocols
- Maintain context across different services

### **Setting Up Context7 MCP Server**

Before running this project, you must ensure that a Context7 MCP server is accessible and configured to provide the `openai-agents` SDK documentation. This server will act as the agent's external knowledge base.

**What is Context7?**

Context7 is a managed context provider service that allows AI agents to access curated documentation and knowledge bases through the MCP protocol. It provides semantic search over documentation, making it perfect for retrieving SDK syntax, API references, and usage examples in real-time.

You can find more information at: https://context7.com/

**MCP Configuration**

Add this configuration to your Gemini CLI config file (typically located at `.gemini/settings.json`):

```json
{
	"mcpServers": {
		"context7": {
			"command": "npx",
			"args": [
				"-y",
				"@upstash/context7-mcp"
			],
			"env": {
				"CONTEXT7_API_KEY": "YOUR_KEY_HERE"
			}
		}
	}
}
```
**Getting Your Context7 API Key:**

1. Visit https://context7.com/
2. Sign up for a free account
3. Navigate to your dashboard to generate an API key
4. Replace `YOUR_KEY_HERE` in the configuration above

**Free Tier Limitations:**

Context7's free tier includes:

- **API Rate Limit:** 60 req/hour
- **Usage Analytics:** Basic

Furthermore you can explore from here: [Contect7 Pricing](https://context7.com/docs/plans-pricing)
Step 2: Create Project
# Create project directory
mkdir chatbot
cd chatbot

# Setup with UV (Python package manager)
# Option 1: Install via pip
pip install uv

# Option 2: Install via PowerShell (Windows)
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"

# Option 3: Install via curl (macOS/Linux)
curl -LsSf https://astral.sh/uv/install.sh | sh

# Initialize project and create virtual environment
uv init
uv venv

# Activate virtual environment
# Windows:
.venv\Scripts\activate

# macOS/Linux:
source .venv/bin/activate

# Install required packages
uv add chainlit python-dotenv openai-agents
**Required Packages:**

- `chainlit` - Web-based chat interface framework
- `python-dotenv` - Environment variable management

- `openai-agents` - OpenAI/Gemini API client library (configured for Gemini API)
Step 3: Create Main gemini.md
# Role: Senior Python AI Engineer

**Objective:** Build a "Personal Chatbot with Memory" using Chainlit and the `openai-agents` SDK.

## 1. Project Overview
The goal is to develop an intelligent web-based chatbot that persists user data (name, preferences, history) across sessions.
* **UI:** Chainlit (Modern, responsive web interface).
* **Model:** Google Gemini model named `gemini-2.0-flash` (via OpenAI Agents SDK).
* **Memory:** Local JSON file storage (accessed via function calling).

## 2. Critical Technical Constraints
**You must adhere to the following strict configuration rules:**

1.  **Zero-Bloat Protocol (CRITICAL):**
    * **Do NOT write extra code.** Do not add bells, whistles, advanced error handling (unless specified), or unnecessary comments.
    * **Focus strictly on the integration:** Connect the `agent` to `chainlit`. Nothing else.
    * **No "Hallucinated" Features:** If it's not in the SDK docs, do not invent it.
2.  **API Configuration:**
    * Use the **OpenAI Agents SDK** Python Library configured for Gemini.
    * **Base URL:** `https://generativelanguage.googleapis.com/v1beta/openai/`
    * **API Key:** Load `GEMINI_API_KEY` from environment variables.
    * **Model:** Use `OpenaiChatCompletionModel` adapted for Gemini.
3.  **SDK Specificity:** You are using `openai-agents` SDK. This is **NOT** the standard `openai` library. You must use the specific syntax provided by the `openai-agents` SDK.
4.  **Error Recovery Protocol:**
    * If you encounter a `SyntaxError`, `ImportError`, or `AttributeError` related to `openai-agents` during development, **STOP**.
    * Do not guess the fix. **You MUST call the `get-library-docs` tool again** to re-read the documentation and verify the correct syntax before rewriting the code.
5.  **Dependency Management:** Use `uv` for package management.

## 3. Architecture & File Structure
*Note: The current directory is the root. Do not create a subfolder named `chatbot`.*

```text
.
├── .env                  # Environment variables
├── tools.py              # Memory management functions (SDK Specific Format)
├── agent.py              # Agent configuration & tool binding
├── app.py                # Chainlit UI & Event Handlers
├── user_profile.json     # JSON Storage (Auto-created if missing)
└── pyproject.toml        # UV Config
````

## 4\. Implementation Steps

**Follow this exact logical flow. Do not skip steps.**

### Step 1: Documentation & Pattern Analysis

**Before writing any code, you must verify the SDK syntax.**

1.  **Action:** Use the MCP tool `get-library-docs` (or `resolve-library-id`) to fetch the official documentation for the **`openai-agents` SDK**.
2.  **Analysis:** Deeply analyze the returned documentation. Look specifically for:
      * How to define tools (decorators vs classes).
      * How to initialize the `Agent`.
      * How to pass the `OpenaiChatCompletionModel` to the agent.
      * **Check:** If you are unsure, query the docs again.

### Step 2: Tool Implementation (`tools.py`)

Create the memory functions **using the strict format found in Step 1**.

  * **Functions:**
      * `read_user_profile()`: Returns dict from `user_profile.json`. Handle `FileNotFoundError` (return empty dict).
      * `update_user_profile(key: str, value: str)`: Updates a specific key in JSON and saves.
  * **Format:** Ensure these are defined as tools recognizable by the `openai-agents` SDK (e.g., using the correct `@tool` decorator or `FunctionTool` wrapper).

### Step 3: Agent Configuration (`agent.py`)

Configure the LLM and Agent using the patterns found in Step 1.

  * Initialize the Gemini client using the Base URL.
  * Initialize the `OpenaiChatCompletionModel` with `gemini-2.0-flash`.
  * **Bind Tools:** Import tools from `tools.py` and register them to the agent instance exactly as the docs prescribe.
  * **System Prompt:** Set instructions to: "Greet users by name if known. Detect when users share personal info and save it using tools."

### Step 4: UI & Application Logic (`app.py`)

Integrate with Chainlit.

  * **`@cl.on_chat_start`**:
      * Initialize the agent.
      * **Display Welcome Message:** Send the **static** message: *"Hello, how can I assist you today?"*
      * **Constraint:** Do NOT inject the username or read the profile for this specific greeting.
  * **`@cl.on_message`**:
      * Pass the user message to the Agent.
      * **Simple Flow (Non-Streaming):** Await the full response from the Agent. Do NOT use streaming mode.
      * Send the final text response to the UI using `cl.Message().send()`.
      * **Debug:** Print/Display tool outputs to verify the agent is actually invoking them.

### Step 5: Environment & Dependencies

  * Create a `.env` template.
  * List necessary packages in `pyproject.toml` (ensure `openai-agents` is included).
  * **Smart Install:** Check `pyproject.toml` and the current environment. **If the dependencies are already installed, DO NOT run the installation commands again.**

## 5\. Testing Scenarios

1.  **New User:** User says "I'm John" -\> Bot saves name -\> Bot replies "Nice to meet you, John."
2.  **Persistence:** Restart server -\> User returns -\> Bot sends static greeting ("Hello, how can I assist you today?") -\> User asks "Do you know my name?" -\> Bot retrieves "John."
3.  **Context Update:** User says "I love Python" -\> Bot updates profile -\> User asks "What do I like?" -\> Bot retrieves "Python."
Prompt for Gemini
Kick off development according to the provided instructions.
Step 4: Implementation Details
### **File: [tools.py](http://tools.py)**

This file contains the two core functions for reading and updating user profiles. It handles JSON file operations safely with error handling.

### **File: [agent.py](http://agent.py)**

This file configures the OpenAI client to use Gemini's API endpoint, defines the function tools schema, and sets up the system prompt for the agent.

### **File: [app.py](http://app.py)**

This is the main Chainlit application that:

- Initializes the agent on chat start
- Loads user profile and greets returning users
   - Handles incoming messages
- Manages tool calls and function execution
- Streams responses back to the user
File: .env
Contains the Gemini API key:
GEMINI_API_KEY=your_api_key_here
Step 5: Running the Application
# Make sure virtual environment is activated
# Windows:
.venv\Scripts\activate

# macOS/Linux:
source .venv/bin/activate

# Run the Chainlit app
chainlit run app.py --watch
The application will start on http://localhost:8000
Step 6: Example Workflow
First-time Interaction:
User: "Hi, my name is Alex."
Agent: [Calls update_user_profile(key='name', value='Alex')]
Agent: "It's nice to meet you, Alex! How can I help you today?"

Subsequent Session:
[User opens app]
Agent: [Calls read_user_profile()]
Agent: "Welcome back, Alex! How can I help you today?"
[User opens app]
Agent: [Calls read_user_profile()]
Agent: "Welcome back, Alex! How can I help you today?"
Preference Saving:
User: "I love pizza and Python programming."
Agent: [Calls update_user_profile(key='favorite_food', value='pizza')]
Agent: [Calls update_user_profile(key='interests', value='Python programming')]
Agent: "Got it! I'll remember that you love pizza and enjoy Python programming."
Information Retrieval:
User: "What do you know about me?"
Agent: [Calls read_user_profile()]
Agent: "Let me see... Your name is Alex, you love pizza, and you're interested in Python programming!"
### **Step 7: Troubleshooting**

**Common Issues:**

1. **Gemini API Errors**
    - Ensure GEMINI_API_KEY is set in `.env`
    - Verify the base URL is correct
    - Check API quota limits
2. **Tool Not Being Called**
    - Review agent's system prompt
    - Check tool definitions match function signatures
    - Ensure conversation provides context for tool use
    3. **JSON File Errors**
    - Verify write permissions in project directory
    - Check for corrupted JSON (validate with JSON linter)
    - Ensure proper encoding (UTF-8)

---

This project provides a solid foundation for building more complex, personalized AI agents with persistent memory! 🎉 
    
    







