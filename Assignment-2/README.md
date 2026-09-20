# Generative AI Assignment 2

This folder contains the completed Smart Mutual Fund Advisor notebook.

## Run locally

1. Install the project dependencies used by the notebook, including LangChain, LangGraph, ChromaDB, Gradio, and the Ollama integration.
2. Install Ollama and pull the required local models:

   ```powershell
   ollama pull nomic-embed-text:latest
   ollama pull llama3.2:latest
   ```

3. Set `GROQ_API_KEY` in the environment if using the default Groq chat model.
4. Open `Generative AI - Assignment 2 (Starter Code).ipynb` from this folder and run the cells in order.

The notebook loads `mutual_funds_data.csv` using a relative path and creates the Chroma database locally, so generated runtime files are intentionally not included in the repository.