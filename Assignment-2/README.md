# Generative AI Assignment 2

## Project Context

This assignment implements a Smart Mutual Fund Advisor using retrieval-augmented generation (RAG). The aim is to build an assistant that can interpret an investor's natural-language preferences, retrieve relevant mutual fund records from a product catalog, and return an explainable portfolio recommendation in a structured format.

The project combines a vector database, an embedding model, an LLM, Pydantic validation, a Gradio interface, and a LangGraph workflow. The design keeps the recommendation grounded in the supplied mutual fund dataset instead of allowing the model to invent products that are not present in the catalog.

## Aims and Goals

The main goals of the assignment are to:

1. Load and inspect a structured mutual fund dataset.
2. Convert CSV records into clean LangChain `Document` objects.
3. Preserve important fund fields in document content and metadata.
4. Create embeddings and store the catalog in ChromaDB.
5. Retrieve the most relevant funds for a user's investment request.
6. Use an LLM to explain the recommendation and select suitable schemes.
7. Validate the LLM response with a Pydantic output schema.
8. Provide a usable Gradio application with model and temperature controls.
9. Rebuild the same RAG process as an explicit LangGraph pipeline.
10. Keep the complete solution reproducible from a clean repository checkout.

This is an educational demonstration of RAG and structured generation. It is not financial advice and does not replace research, risk assessment, or advice from a qualified financial professional.

## User Experience

The application accepts a free-form description of investment needs, for example:

```text
I want a low-risk investment portfolio.
```

It returns:

- A short explanation of why the selected schemes match the request.
- A list of recommended scheme names and their fund houses.
- Structured scheme details including scheme type and scheme category.

The advanced settings section allows a developer to choose the chat model and adjust temperature between 0 and 2. A lower temperature is recommended for more consistent structured output.

## Solution Architecture

### 1. Data loading and preprocessing

The notebook loads `mutual_funds_data.csv` with LangChain's `CSVLoader`. Each row is converted from the loader's text representation into a dictionary. The following fields are retained in the document content:

- Scheme name
- Fund house
- Scheme type
- Scheme category
- Net asset value
- Date

Scheme type and scheme category are also stored as Chroma metadata. The CSV uses lowercase field names, so the preprocessing step normalizes access to keys such as `scheme_name`, `fund_house`, and `net_asset_value`.

### 2. Embeddings and vector database

The solution uses Ollama's local `nomic-embed-text:latest` model to create embeddings. The embedded documents are stored in a persistent Chroma collection named `mutual_funds`. To keep local processing manageable, the notebook indexes up to 1,000 records in batches of 100.

The Chroma database is generated locally at runtime and is intentionally excluded from the repository because it is a machine-specific runtime artifact. Running the storage cell recreates or updates the local collection.

### 3. Structured response schema

The LLM response is validated with two Pydantic models:

- `MutualFund`: scheme name, fund house, scheme type, and scheme category.
- `MutualFundOutput`: recommendation reasoning and a list of `MutualFund` objects.

The prompt instructs the model to use only the retrieved context and to avoid selecting two schemes with the same fund house and scheme type combination.

### 4. RAG pipeline

The standard pipeline follows this sequence:

```text
User preferences
   |
   v
Chroma similarity search
   |
   v
Retrieved fund context
   |
   v
Prompt + selected chat model
   |
   v
Pydantic output parser
   |
   v
Validated recommendation
```

The `generate_portfolio` function performs retrieval, builds the prompt context, selects the requested model, invokes the prompt chain, and returns the validated Pydantic result. Parser failures are handled with a clear message rather than exposing a raw exception to the Gradio interface.

### 5. LangGraph implementation

The second part of the notebook expresses the same workflow as a LangGraph state machine:

```text
START -> retrieve -> generate -> END
```

The graph state contains:

- `preferences`: the user's request
- `context`: retrieved LangChain documents
- `answer`: the structured recommendation or an error message

The retrieval node searches Chroma. The generation node joins the retrieved documents, invokes the selected LLM through the prompt and Pydantic parser, and stores the answer in the graph state. The manual graph test confirms that retrieved documents and a structured recommendation are produced.

## Models and Configuration

The notebook supports these model choices:

- `GPT-OSS-120B (Groq)`: hosted chat model; requires `GROQ_API_KEY`.
- `Grok (xAI)`: requires the corresponding xAI credentials and model access.
- `Llama-3.2 (Ollama)`: local chat model; requires Ollama and `llama3.2:latest`.

The embedding model is independent of the chat model and uses `nomic-embed-text:latest` through Ollama.

## Repository Contents

- `Generative AI - Assignment 2 (Starter Code).ipynb`: completed implementation and demonstrations.
- `mutual_funds_data.csv`: mutual fund product catalog used for retrieval.
- `Generative AI - Assignment 2.pdf`: assignment brief.
- `requirements.txt`: Python packages required by the notebook.
- `README.md`: project context, architecture, setup, and execution notes.

## Run Locally

Open a terminal in this `Assignment-2` folder and install the dependencies:

```powershell
python -m pip install -r requirements.txt
```

Install Ollama and pull the local models:

```powershell
ollama pull nomic-embed-text:latest
ollama pull llama3.2:latest
```

For the default Groq model, set the API key in the environment before opening the notebook:

```powershell
$env:GROQ_API_KEY = "your-key"
```

Open `Generative AI - Assignment 2 (Starter Code).ipynb` from this folder and run the cells in order. Running from the folder is important because the notebook uses the relative path `mutual_funds_data.csv`.

## Expected Execution Order

1. Install or verify the Python packages.
2. Import the LangChain, LangGraph, Chroma, Gradio, and Pydantic components.
3. Load environment variables and configure the model map.
4. Initialize embeddings and Chroma.
5. Load and preprocess the CSV records.
6. Add the documents to Chroma in batches.
7. Initialize the retriever and Pydantic schema.
8. Define the prompt and test the standard RAG pipeline.
9. Define and test `generate_portfolio`.
10. Launch the first Gradio interface if required.
11. Define the LangGraph state, nodes, edges, and compiled graph.
12. Run the manual LangGraph test and launch the graph-based interface.

## Reproducibility Notes

The notebook has been tested with Python 3.11, LangChain, LangGraph, ChromaDB, Gradio, and Ollama. API-backed models require valid provider credentials and may have availability or quota limits. Local Ollama models require the Ollama service to be running. The generated Chroma files are not committed, so each new machine should run the document indexing cell before performing similarity searches.