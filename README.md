# StockPicker

A modular, agent-driven pipeline to discover trending companies in a specified sector, perform in-depth financial research, and select the best investment opportunity. Built using the `crewai` framework, this project orchestrates multiple AI agents—each with clear roles and tasks—to automate the end-to-end stock selection process.

---

## Table of Contents

1. [Features](#features)
2. [Architecture](#architecture)
3. [Getting Started](#getting-started)

   * [Prerequisites](#prerequisites)
   * [Installation](#installation)
   * [Configuration](#configuration)
4. [Usage](#usage)
5. [Project Structure](#project-structure)
6. [Agent & Task Configuration](#agent--task-configuration)

   * [`config/agents.yaml`](#configagentsyaml)
   * [`config/tasks.yaml`](#configtasksyaml)
7. [Customization](#customization)
8. [Contributing](#contributing)
9. [License](#license)

---

## Features

* **Trending Company Finder**: Scans the latest news to identify 2–3 emerging companies in a given sector.
* **Financial Researcher**: Generates detailed reports on each trending company’s market position, outlook, and investment potential.
* **Stock Picker**: Synthesizes research to choose the best company and issues a push notification with rationale.

---

## Architecture

This project leverages the `crewai` orchestration framework:

1. A **manager** agent delegates subtasks to specialized agents.
2. **Agents** (`trending_company_finder`, `financial_researcher`, `stock_picker`) each run distinct steps in the pipeline, defined via YAML configs.
3. **Tasks** (`find_trending_companies`, `research_trending_companies`, `pick_best_company`) encapsulate the inputs, outputs, and hand-off between agents.
4. **Memory Layers**:

   * **Long-Term Memory** persists across runs via SQLite.
   * **Short-Term Memory** uses a RAG-based storage.
   * **Entity Memory** tracks key entities over the session.

---

## Getting Started

### Prerequisites

* **Python ≥ 3.8**
* An OpenAI API key in `OPENAI_API_KEY` environment variable.
* (Optional) Push notification credentials for `PushNotificationTool`.

### Installation

```bash
git clone https://github.com/yourusername/stock-picker.git
cd stock-picker
python -m venv venv
source venv/bin/activate         # Unix/macOS
venv\Scripts\activate.bat        # Windows
pip install -r requirements.txt  # see below for core deps
```

<details>
<summary><strong>Core Dependencies</strong></summary>

```text
crewai
crewai-tools
crewai-memory
crewai-memory-rag-storage
crewai-memory-ltm-sqlite-storage
pydantic
serper-dev-tool
pysbd
```

</details>

### Configuration

All agent and task behaviors are driven by YAML files in the `config/` directory:

* **Agents**: `config/agents.yaml`&#x20;
* **Tasks**: `config/tasks.yaml`&#x20;

Be sure to update roles, goals, and backstories as needed, and adjust output paths if required.

---

## Usage

By default, the pipeline targets the **Technology** sector. To run:

```bash
python main.py
```

This executes the full crew:

1. **Find Trending Companies**
2. **Research Trending Companies**
3. **Pick Best Company**

Output files:

* `output/trending_companies.json`
* `output/research_report.json`
* `output/decision.md`

Final decision is also printed to the console via `main.py` .

---

## Project Structure

```
.
├── config/
│   ├── agents.yaml       # AI agent definitions
│   └── tasks.yaml        # Task workflows & outputs
├── memory/
│   ├── long_term_memory_storage.db
│   └── RAG embeddings…
├── output/
│   ├── trending_companies.json
│   ├── research_report.json
│   └── decision.md
├── stock_picker/
│   ├── __init__.py
│   ├── crew.py           # Crew definition & wiring :contentReference[oaicite:3]{index=3}
│   ├── tools/
│   │   └── push_tool.py  # Push notification integration
│   └── ...
├── main.py               # Entry point :contentReference[oaicite:4]{index=4}
└── requirements.txt
```

---

## Agent & Task Configuration

### `config/agents.yaml`

Defines each AI agent’s **role**, **goal**, **backstory**, and LLM model:

```yaml
trending_company_finder:
  role: >-
    Financial News Analyst that finds trending companies in {sector}
  goal: >-
    You read the latest news, then find 2-3 companies...
  backstory: >-
    You are a market expert...
  llm: openai/gpt-4o-mini

# ... other agents ...
```

### `config/tasks.yaml`

Specifies the workflow steps, linking tasks to agents and output targets:

```yaml
find_trending_companies:
  description: >-
    Find the top trending companies...
  agent: trending_company_finder
  output_file: output/trending_companies.json

research_trending_companies:
  context:
    - find_trending_companies
  output_file: output/research_report.json

pick_best_company:
  context:
    - research_trending_companies
  output_file: output/decision.md
```

---

## Customization

* **Sector**: Change the default in `main.py` or accept CLI args.
* **Memory Storage**: Swap out `LTMSQLiteStorage` or RAG configs in `crew.py`.
* **Notification**: Integrate your own push service by editing `push_tool.py`.
* **Models**: Upgrade or swap LLMs in `agents.yaml`.

---

## Contributing

1. Fork the repo & create a feature branch.
2. Run tests (if added) and ensure linting passes.
3. Submit a pull request with clear descriptions.

---

## License

MIT License © 2025 Rishi Kora.

---

*Happy investing!*
