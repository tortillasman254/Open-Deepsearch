# Open-Deepsearch

This project implements a multi-agent system using CrewAI to research topics online. It combines the power of Jina AI's DeepSearch for broad semantic search with the optional capability of using the `browser-use` library for autonomous, interactive web navigation. The system is configurable via YAML and `.env` files.

## Features

*   **CrewAI Orchestration:** Uses CrewAI agents (Researcher, Writer) and tasks for a structured workflow.
*   **Jina DeepSearch Integration:** Leverges Jina AI DeepSearch via a custom tool for initial information gathering and source identification. [1]
*   **Optional Browser Navigation:** Integrates the powerful `browser-use` library [2] as a tool, allowing the researcher agent to visit specific URLs, interact with pages, and extract targeted information when Jina's results are insufficient. This is configurable.
*   **Configurable LLM:** Uses OpenRouter [1] to access a wide variety of language models (configurable via `config.yaml`).
*   **External Configuration:** Most settings (LLM model, tool parameters, logging levels, Crew behavior) are managed via `config.yaml` [1][2]. Secrets (API keys) are stored in `.env`.
*   **Configurable Logging:** Adjust logging levels for the script and internal libraries via `config.yaml`.
*   **Asynchronous Handling:** Includes `nest_asyncio` to help manage asynchronous operations required by `browser-use` within potentially synchronous contexts.

## Requirements

*   **Python:** Version 3.11 or higher (strictly required if using the `browser-use` tool [2]).
*   **pip:** Python package installer.
*   **Git:** (Optional) For cloning the repository.
*   **API Keys:**
    *   **OpenRouter API Key:** Required for the LLM. Get one at [https://openrouter.ai/keys](https://openrouter.ai/keys). [1]
    *   **Jina API Key:** Optional, but recommended for higher DeepSearch rate limits. Get one from Jina AI.
    *   **OpenAI API Key:** Optional, only if you modify the code to test `browser-use` with OpenAI directly.

## Setup

1.  **Clone or Download:**
    ```bash
    # If using Git
    git clone <repository_url>
    cd <repository_directory>
    # Or, simply download/copy the .py, .yaml, and .env files to a directory
    ```

2.  **Create a Virtual Environment (Recommended):**
    ```bash
    python -m venv .venv
    # Activate the environment
    # On Windows:
    .\.venv\Scripts\activate
    # On macOS/Linux:
    source .venv/bin/activate
    ```

3.  **Install Dependencies:**
    ```bash
    pip install -r requirements.txt
    # Or install manually:
    # pip install crewai crewai-tools python-dotenv PyYAML langchain_openai requests nest_asyncio
    ```
    *   **If Enabling `browser-use`:** You *also* need to install `browser-use` and its Playwright browsers:
        ```bash
        pip install browser-use playwright
        playwright install --with-deps
        ```
        *(The `--with-deps` flag helps install necessary browser dependencies on some systems).*

4.  **Create `.env` File:**
    *   Create a file named `.env` in the project directory.
    *   Copy the contents from the example `.env` file above.
    *   Replace placeholder values with your actual API keys. **Never commit this file to version control.**

5.  **Create and Customize `config.yaml`:**
    *   Create a file named `config.yaml` in the project directory.
    *   Copy the contents from the example `config.yaml` file above.
    *   Adjust the settings as needed:
        *   `llm.model_name`: Choose your desired OpenRouter model [1]. Test different ones if you encounter issues (especially with `browser-use`). `openai/gpt-4o` or `anthropic/claude-3.5-sonnet` are generally robust choices for agentic tasks.
        *   `tools.browser_use.enabled`: Set to `true` to enable the browser tool, `false` to disable it.
        *   Adjust other tool parameters, logging levels, and crew settings as desired.

## Usage

1.  **Activate Virtual Environment** (if you created one):
    ```bash
    # Windows: .\.venv\Scripts\activate
    # macOS/Linux: source .venv/bin/activate
    ```

2.  **Run the Script:**
    ```bash
    python crew_configurable.py
    ```

3.  **Enter Topic:** The script will prompt you to enter the research topic:
    ```
    What topic do you want to research? <Your Topic Here>
    ```

4.  **Observe Output:** The script will log its progress, including agent thoughts and actions (if `crew.verbose` is `true` in `config.yaml`), tool usage, and finally, the final researched answer produced by the Writer agent.

## Configuration Details

*   **`.env`:** Stores sensitive API keys. Keep this file secure.
*   **`config.yaml`:** Controls non-sensitive parameters like LLM selection [1], tool behavior [1][2], logging levels, and CrewAI settings. Modify this file to tune the agent's performance and capabilities.

## Troubleshooting

*   **`ValidationError` / `Field required`:** Often related to Pydantic initialization in custom tools. Ensure `super().__init__(**kwargs)` is called correctly.
*   **`ImportError: cannot import name '...'`:** Check if all required libraries are installed (`pip install ...`). If related to `browser-use`, make sure it's installed and `tools.browser_use.enabled` is `true`.
*   **BrowserUse Loops (`Step 1` repeatedly):**
    *   **Most likely:** The selected LLM (`llm.model_name` in `config.yaml` [1]) is not capable enough or not fully compatible with `browser-use`. Try a more powerful model (e.g., `openai/gpt-4o`).
    *   Ensure your OpenRouter API key is valid and the account is active.
    *   Ensure Playwright is installed (`playwright install`).
    *   Ensure Python version is >= 3.11.
*   **API Errors (401 Unauthorized, 403 Forbidden, etc.):** Double-check your API keys in `.env`. Verify account status on OpenRouter/Jina AI.
*   **Timeout Errors:** Network issues or the called API (Jina/OpenRouter/Target Website) is slow. Check internet connection. You might increase timeout values in the code if needed, but address the root cause if possible.
# Open-Deepsearch
