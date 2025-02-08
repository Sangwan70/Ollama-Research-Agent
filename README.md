# Ollama Research Agent 

Ollama Research Agent  is an advanced, fully local web research assistant designed to leverage any Large Language Model (LLM) hosted by [Ollama](https://ollama.com/search). This tool facilitates comprehensive research by generating web search queries based on user-defined topics, gathering search results via [Tavily](https://www.tavily.com/), and summarizing the findings. It employs an iterative process to refine its understanding: after summarizing the initial results, it reflects on potential knowledge gaps, formulates new search queries to address these gaps, and conducts additional searches. This cycle repeats for a user-specified number of iterations, ensuring a thorough and well-rounded exploration of the topic.

The final output is a detailed markdown summary that includes all sources used during the research process. By integrating Open Source Large Language Models, Ollama Deep Researcher offers a powerful, customizable, and privacy-focused solution for in-depth web research. Its ability to autonomously refine queries and improve summaries makes it an invaluable tool for users seeking accurate and comprehensive information on complex subjects.

![research-rabbit](https://github.com/user-attachments/assets/4308ee9c-abf3-4abb-9d1e-83e7c2c3f187)

Short summary:
<video src="https://github.com/user-attachments/assets/02084902-f067-4658-9683-ff312cab7944" controls></video>

## 📺 Video Lectures

See it in action or build it yourself? Check out these helpful video tutorials:
- [Overview of Ollama-Research-Agent with DeepSeek R1](https://www.youtube.com/#) - Load and test [DeepSeek R1](https://api-docs.deepseek.com/news/news250120) [distilled models](https://ollama.com/library/deepseek-r1).
- [Building Ollama Research Agent from Scratch](https://#) - Overview of how this is built.

## 🚀 Quickstart

### Mac 

1. Download the Ollama app for Mac [here](https://ollama.com/download).

2. Pull a local LLM from [Ollama](https://ollama.com/search). As an [example](https://ollama.com/library/deepseek-r1:8b): 
```bash
ollama pull deepseek-r1:8b
```

3. Clone the repository:
```bash
git clone https://github.com/Sangwan70/Ollama-Research-Agent.git
cd Ollama-Research-Agent
```

4. Get your web Search API from  [Tavily API](https://tavily.com/)


5. Copy the example environment file:
```bash
cp .env.example .env
```

6. Edit the `.env` file with your preferred text editor and add your API key:
```bash
# Required: Add search provider API key
TAVILY_API_KEY=tvly-xxxxx      # Get your key at https://tavily.com
```

Note: If you prefer using environment variables directly, you can set them in your shell:
```bash
export TAVILY_API_KEY=tvly-xxxxx
```

After setting the key, verify they're available:
```bash
echo $TAVILY_API_KEY  # Should show your API key
```

7. (Recommended) Create a virtual environment:
```bash
python -m venv .venv
source .venv/bin/activate
```

8. Launch the assistant with the LangGraph server:

```bash
# Install uv package manager
curl -LsSf https://astral.sh/uv/install.sh | sh
uvx --refresh --from "langgraph-cli[inmem]" --with-editable . --python 3.12 langgraph dev
```

### Windows 

1. Download the Ollama app for Windows [here](https://ollama.com/download).

2. Pull a local LLM from [Ollama](https://ollama.com/search). As an [example](https://ollama.com/library/deepseek-r1:8b): 
```powershell
ollama pull deepseek-r1:8b
```

3. Clone the repository:
```bash
git clone https://github.com/Sangwan70/Ollama-Research-Agent.git
cd Ollama-Research-Agent
```
 
4. Get your web Search API from  [Tavily API](https://tavily.com/)

5. Copy the example environment file:
```bash
cp .env.example .env
```

Edit the `.env` file with your preferred text editor and add your API keys:
```bash
# Required: Add search provider API key
TAVILY_API_KEY=tvly-xxxxx      # Get your key at https://tavily.com
```

Note: If you prefer using environment variables directly, you can set them in Windows (via System Properties or PowerShell):

```bash
export TAVILY_API_KEY=<your_tavily_api_key>
```

Crucially, restart your terminal/IDE (or sometimes even your computer) after setting it for the change to take effect. After setting the keys, verify they're available:
```bash
echo $TAVILY_API_KEY  # Should show your API key
```

7. (Recommended) Create a virtual environment: Install `Python 3.12` (and add to PATH during installation). Restart your terminal to ensure Python is available, then create and activate a virtual environment:

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

8. Launch the assistant with the LangGraph server:

```powershell
# Install dependencies 
pip install -e .
pip install langgraph-cli[inmem]

# Start the LangGraph server
langgraph dev
```

### Using the LangGraph Studio UI 

When you launch LangGraph server, you should see the following output and Studio will open in your browser:
> Ready!
> 
> API: http://127.0.0.1:2024
> 
> Docs: http://127.0.0.1:2024/docs
> 
> LangGraph Studio Web UI: https://smith.langchain.com/studio/?baseUrl=http://127.0.0.1:2024

Open `LangGraph Studio Web UI` via the URL in the output above. 

In the `configuration` tab:
* Verify your web search tool `Tavily`
* Set the name of your local LLM to use with Ollama (it will by default be `llama3.2`) 
* You can set the depth of the research iterations (it will by default be `3`)

<img width="1621" alt="Screenshot 2025-01-24 at 10 08 31 PM" src="https://github.com/user-attachments/assets/7cfd0e04-28fd-4cfa-aee5-9a556d74ab21" />

Give the assistant a topic for research, and you can visualize its process!

<img width="1621" alt="Screenshot 2025-01-24 at 10 08 22 PM" src="https://github.com/user-attachments/assets/4de6bd89-4f3b-424c-a9cb-70ebd3d45c5f" />

## How it works

Ollama Research Agent is inspired by [IterDRAG](https://arxiv.org/html/2410.04343v1#:~:text=To%20tackle%20this%20issue%2C%20we,used%20to%20generate%20intermediate%20answers.). This approach will decompose a query into sub-queries, retrieve documents for each one, answer the sub-query, and then build on the answer by retrieving docs for the second sub-query. Here, we do similar:
- Given a user-provided topic, use a local LLM (via [Ollama](https://ollama.com/search)) to generate a web search query
- Uses a search engine (configured for [Tavily](https://www.tavily.com/)) to find relevant sources
- Uses LLM to summarize the findings from web search related to the user-provided research topic
- Then, it uses the LLM to reflect on the summary, identifying knowledge gaps
- It generates a new search query to address the knowledge gaps
- The process repeats, with the summary being iteratively updated with new information from web search
- It will repeat down the research rabbit hole 
- Runs for a configurable number of iterations (see `configuration` tab)  

## Outputs

The output of the graph is a markdown file containing the research summary, with citations to the sources used.

All sources gathered during research are saved to the graph state. 

You can visualize them in the graph state, which is visible in LangGraph Studio:

![Screenshot 2024-12-05 at 4 08 59 PM](https://github.com/user-attachments/assets/e8ac1c0b-9acb-4a75-8c15-4e677e92f6cb)

The final summary is saved to the graph state as well: 

![Screenshot 2024-12-05 at 4 10 11 PM](https://github.com/user-attachments/assets/f6d997d5-9de5-495f-8556-7d3891f6bc96)
