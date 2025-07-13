<prompt>
    <persona>
        You are an expert Project Planning and Code Generation Prompt Engineer. Your primary goal is to transform a project concept into a series of precise, actionable, and test-driven prompts for a separate code-generation LLM. You excel at breaking down complex tasks into the smallest feasible, yet impactful, iterative steps.
    </persona>

    <context>
        The user will provide a technical specification. Your task is to meticulously plan its implementation by generating a sequence of prompts. These prompts will guide a code-generation LLM to build the project incrementally, ensuring each step is testable and integrates seamlessly with previous steps.
    </context>

    <instructions>
        <planning_phase>
            1.  **Understand the Project:** First, thoroughly analyze the provided project specification.
            2.  **Develop Blueprint:** Draft a detailed, step-by-step conceptual blueprint for building the project. This is your high-level plan.
            3.  **Initial Chunking:** Break down this blueprint into small, iterative functional chunks that logically build upon each other.
            4.  **Granular Breakdown:** Take each chunk and break it down further into even smaller, specific steps. Each step should be a distinct task.
            5.  **Review and Refine Steps:**
                *   Ensure steps are small enough to be implemented and tested safely and independently.
                *   Ensure steps are large enough to represent meaningful progress.
                *   Iterate on this breakdown (steps 3-5) until the granularity feels appropriate for safe, test-driven development without excessive overhead.
        </planning_phase>

        <prompt_generation_phase>
            1.  **Foundation:** Based on the refined steps, generate a series of prompts for a code-generation LLM.
            2.  **Test-Driven Development:** Each prompt must guide the code-generation LLM to implement its corresponding step in a test-driven manner. This means the prompt should ask for tests first, then the code to make the tests pass.
            3.  **Best Practices:** Emphasize coding best practices, incremental progress, and early, frequent testing.
            4.  **Complexity Management:** Ensure no significant jumps in complexity between steps.
            5.  **Continuity:** Each new prompt must build upon the code and tests generated from previous prompts. All code generated should be integrated; no orphaned or hanging code.
            6.  **Final Integration:** The sequence of prompts should naturally lead to wiring all implemented pieces together.
        </prompt_generation_phase>

        <output_format>
            1.  **Separate Sections:** Clearly separate each generated prompt.
            2.  **Markdown:** Use Markdown for all output.
            3.  **Code Tags for Prompts:** Enclose each generated prompt intended for the code-generation LLM within text-based code tags (e.g., ```text ... ```).
            4.  **Context and Rationale:** Briefly include your reasoning or context *before* each generated prompt if it aids clarity for the user who will be feeding these prompts to the code-generation LLM.
        </output_format>
    </instructions>

    <technical_specification>
# **Technical Specification: PromptPoppins**

*Version 9.0 - Final, Complete*

## **1. Overview**

PromptPoppins is an intelligent prompt management application that reliably captures clipboard content, uses a queue-based system to process prompts via embedded or cloud LLMs, and stores them in a transparent, file-system-based repository with per-prompt usage tracking.

## **2. Scoping**

### **2.1. Goal**

To create a robust system that automatically captures, versions, and organizes a user's LLM prompts. The system will enable users to track prompt evolution, reuse them effectively, and understand their own usage patterns.

### **2.2. Target Users**

* **Professionals**: Software engineers and knowledge workers using AI daily.
* **Educators**: Those teaching advanced AI literacy and prompting techniques.
* **Parents**: Individuals using prompts as a tool to raise AI-literate children.

### **2.3. Success Metrics**

Key metrics will be tracked to measure user engagement and value:

* **Total Prompts Saved**: The total number of unique prompts created in the repository.
* **Total Reuses**: The total number of times any existing prompt is reused.
* **Per-Prompt Reuse Count**: A specific usage counter for each individual prompt file.

## **3. Functional Requirements (Features)**

1. **Automated Prompt Capture**: The application silently monitors the clipboard, using a user-configurable LLM to automatically detect and identify text that is a prompt.
2. **Zero-Touch File Saving**: Identified prompts are automatically saved as markdown files (.md) to a local file system repository without requiring user confirmation.
3. **AI-Powered File Organization**: An "Organizer" LLM determines the best directory for the saved prompt. It has the authority to create new folders and refactor the existing folder structure.
4. **AI-Powered Naming & Versioning**: A "Namer/Versioner" LLM intelligently names the new file based on its content and context. If a captured prompt is an exact duplicate of an existing file's content, a new file will not be created; instead, the 'last modified' timestamp of the existing file will be updated.
5. **Direct File System Access**: The primary application UI will be minimal, featuring a main button (e.g., "Show in Finder") that takes the user directly to their prompt repository.
6. **Integrated Settings UI**: An integrated application view (e.g., a "Settings" window) will serve as the interface for advanced configuration.
7. **AI Agent Customization**: Through the settings UI, users can provide custom instructions (system prompts) to guide the behavior of the agents.
8. **Pluggable AI Models**: Users can select the LLM for each AI task, choosing between API-based cloud models and a file-based, embedded local model.
9. **Automated Changelog**: The application will maintain a human-readable, timestamped log file (_journal.log) in the root of the repository, documenting all successful file system operations performed by the AI agents.
10. **Per-Prompt Usage Tracking**: When a captured prompt is identified as a reuse, the system will increment a specific reuse_count counter for that prompt in the similarity index file.
11. **Durable Prompt Queuing**: After a prompt is identified, it is immediately saved to a temporary processing queue on disk to ensure no prompts are lost if the application quits unexpectedly.
12. **System Tray Utility**: The application will provide a cross-platform system tray icon. Clicking this icon will display a menu containing a list of the most recent prompts for quick copying, and shortcuts to open the settings or quit the application.
13. **First-Launch Onboarding**: The application will provide a guided setup on first launch to ensure a smooth user experience.

## **4. Technical Design**

### **4.1. Data Model & Storage**

* **Repository**: A local directory (e.g., ~/PromptPoppins/).
* **Secrets**: A .env file for user API keys.
* **Agent Configuration**: A .toml file for each agent (e.g., agent-identify.toml).
  * **Schema Example (agent-naming.toml):**
    provider = "local"
    model_path = "/path/to/model.gguf"
    api_key_env_var = "OPENAI_API_KEY"
    model_name = "gpt-4o-mini"
    max_output_tokens = 20
    system_prompt = "You are an expert at creating descriptive, concise, snake_case filenames..."

* **Similarity Index**: A binary cache file (prompt_index.cache) storing a map where the key is the file path and the value contains its SHA-256 hash, MinHash signature, and a reuse_count integer.
* **Processing Queue**: A hidden directory (/.queue/) in the repository root.

### **4.2. Core Logic**

* **Capture & Queue Workflow**:
  1. The [Identifier] agent saves identified prompts to the /.queue/ directory.
  2. An asynchronous background processor picks up queued files for processing.
  3. On startup, the processor will clear any files remaining in the queue.
* **Similarity Detection**: Multi-stage process using SHA-256 and MinHash to find candidates before invoking an LLM.
* **Pluggable LLM Service**: A common LlmClient trait in Rust will be implemented for Cloud Providers (OpenAIClient, AnthropicClient, GeminiClient) and an Embedded Provider (LocalLlmClient using llama-cpp-rs).

### **4.3. API Contracts (UI ↔ Rust Backend)**

* get_settings()
* set_settings(payload)
* show_in_file_explorer()
* get_repository_path()
* get_recent_prompts(limit: int)

### **4.4. Onboarding & Error Handling**

* **First-Launch Onboarding**:
  1. On first run, the app will automatically create the ~/PromptPoppins/ directory structure and default configuration files.
  2. The main UI will display a welcome message guiding the user to the Settings page to configure an AI model.
* **Error Handling Flow**:
  1. **Notification**: On configuration or runtime error (e.g., invalid API key, failed model load), the system tray icon will dynamically change to an "error state" icon (e.g., tray-icon-error.png).
  2. **Tooltip**: The tooltip for the error icon will read "Error detected. Open Settings for details."
  3. **Diagnostics**: The Settings UI will contain a dedicated, visible status area that displays a clear, human-readable error message detailing the problem and its source.

### **4.5. Recommended Components**

* **Framework**: Tauri
* **UI Framework**: React, Vue, or Svelte.
* **Backend**: Rust
* **Clipboard**: tauri-clipboard-manager plugin.
* **Embedded Local LLM**: llama-cpp-rs.
* **Hashing**: A standard SHA-256 library and a MinHash library like rensa.

### **4.6. Non-Functional Requirements (NFRs)**

* **Security**: The .env file will contain a prominent comment warning the user against sharing or committing the file.
* **Performance**: The application must have minimal resource usage when idle. The similarity detection process must be performant.
* **Dependencies**: The final compiled application must be a self-contained binary with no requirement for external runtimes.    </technical_specification>

    <final_task>
        Based on the technical specification above, and following all instructions meticulously, generate the structured output containing the context, rationale, and the series of prompts for the code-generation LLM.
    </final_task>
</prompt>