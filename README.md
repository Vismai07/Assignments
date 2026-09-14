# Generative AI - Assignment 1

This folder contains the files submitted for Assignment 1. The work demonstrates how to use a local large language model with LangChain to classify text and extract structured information from datasets.

## Contents

- `BBC_news.ipynb`: Loads the BBC news dataset, limits the working data to 30 articles, and classifies articles into Business, Entertainment, Politics, Sport, or Tech.
- `bbc-news-data.csv`: BBC news dataset used by the news classification notebook.
- `Job_posting.ipynb`: Loads job postings, classifies job domains, and extracts skills, education, and experience requirements.
- `job_title_des.csv`: Job title and job description dataset used by the job-posting notebook.
- `Generative AI - Assignment 1.pdf`: Assignment brief.

## Job Posting Work Completed

The job-posting notebook accomplishes the following:

1. Loads the job-posting CSV into a Pandas DataFrame.
2. Limits processing to the first 25 postings to keep local model processing feasible.
3. Builds a LangChain prompt to classify each role into one category:
	`Technology/IT`, `Finance`, `Marketing`, `Healthcare`, `Education`, or `Others`.
4. Tests the classification prompt on a sample posting.
5. Builds a structured extraction prompt for:
	- `Required_Skills`
	- `Education_Required`
	- `Experience_Required`
6. Uses `Not specified` when a requirement is not mentioned in the posting.
7. Applies classification and extraction to all 25 selected postings.
8. Joins the model results back to the original DataFrame as new columns:
	`Predicted_Category`, `Required_Skills`, `Education_Required`, and `Experience_Required`.
9. Validates that the final DataFrame contains 25 rows, all required columns, and valid category labels.

The completed run produced an enriched DataFrame containing the original job title and description columns together with the four generated requirement fields.

## BBC News Work Completed

The BBC news notebook implements a complete article-processing workflow:

1. Loads `bbc-news-data.csv` with its tab-separated format into a Pandas DataFrame.
2. Limits the working dataset to the first 30 articles.
3. Classifies each article into one of five topics: `Business`, `Entertainment`, `Politics`, `Sport`, or `Tech`.
4. Tests the topic-classification prompt on a sample article and compares the prediction with the article's original category.
5. Generates an objective two- to three-sentence summary for every selected article.
6. Extracts named entities and groups them into `People`, `Organizations`, and `Places`.
7. Handles empty article text and rejects invalid topic labels during processing.
8. Adds the generated results to the DataFrame using the columns `Detected_Topic`, `Summary`, and `Key_Entities`.
9. Displays both the generated columns and the complete final DataFrame containing the original article data.

This demonstrates how one LangChain workflow can combine classification, summarization, and structured information extraction over a collection of news articles.

## Tools Used

- Python and Pandas for data loading and DataFrame processing
- LangChain for prompt templates and output parsing
- Ollama with the `llama3.2` model for local LLM inference
- JSON parsing and validation for structured requirements extraction

## Running the Notebooks

1. Install the project dependencies and ensure Ollama is installed.
2. Pull the model with `ollama pull llama3.2`.
3. Open the required notebook in Jupyter or VS Code.
4. Run the cells in order from the notebook directory so the relative dataset paths resolve correctly.
