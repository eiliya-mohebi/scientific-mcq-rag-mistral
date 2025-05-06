# Turing Apex Challenge - Scientific Question Answering Solution

This repository contains a solution developed for the Turing Apex Challenge, focused on answering scientific multiple-choice questions using a Large Language Model (LLM) enhanced with Retrieval-Augmented Generation (RAG).

## Solution Approach

The core approach utilizes the following components within a Google Colab environment:

1.  **LLM:** Using opensource `mistralai/Mistral-7B-Instruct-v0.3` (loaded with 4-bit quantization for efficiency) as the main LLM.
2.  **RAG Source:** Wikipedia, accessed via the `wikipedia` Python library and LangChain's `WikipediaRetriever`.
3.  **Retrieval Enhancement:** LLM-based **Query Expansion** is used. The original question is fed to the LLM to generate multiple related search queries aiming to retrieve a better and more relevant set of context documents from Wikipedia. Documents retrieved from the original and expanded queries are combined.
4.  **Prompting Technique:**
    * **Few-Shot Learning:** Provides 5 random examples within the prompt to guide the model's response format and reasoning style.
    * **Chain-of-Thought (CoT):** The prompt diretly instructs the model to "think step-by-step" and explain its reasoning *before* providing the final answer line. This encourages more structured problem-solving.
5.  **Output Parsing:** A Python function parses the LLM's raw output to reliably extract only the single uppercase letter (A-E) following the "LLM Answer:" marker, handling potential variations like newlines or extra text.

## Challenges Faced and Solutions

1.  **Challenge:** Initial retrieval using only the original question sometimes fetched irrelevant or insufficient context from Wikipedia for complex scientific topics.
    * **Solution:** Implemented **LLM-based Query Expansion**. Generating multiple search queries based on the original question helped retrieve a better set of documents, increasing the chances of finding the necessary information. Context from all queries was combined.

2.  **Challenge:** The LLM occasionally failed to handle the required output format (`LLM Answer: [Letter]`), sometimes adding explanations or placing the letter incorrectly.
    * **Solution:** Developed a **robust output parser function** (`parse_llm_output`) using regular expressions. It specifically targets the expected pattern, handles leading/trailing whitespace and newlines after the prefix.
3.  **Challenge:** Ensuring the relatively small Mistral-7B model performed enough reasoning for complex scientific questions based on the retrieved context.
    * **Solution:** Used **Chain-of-Thought (CoT)** prompting. Directly asking the model to generate step-by-step reasoning before the final answer improved its ability to process the context and arrive at a more logical conclusion. `max_new_tokens` for the LLM was increased to for reasoning process.

## Potential Future Improvements

While the current solution aims to maximize performance within Colab constraints, further accuracy improvements could likely be achieved through:

1.  **Using Larger/More Advanced LLMs:** Models like Llama 3 (8B, 70B), Gemma 2 (9B, 27B), or Mixtral often show stronger reasoning capabilities but typically require more computational resources than available in standard Colab.
2.  **Vector Database RAG:** Implementing a more complex RAG approach using vector embeddings (ChromaDB) and semantic search. This could involve embedding chunks of relevant scientific texts (from Wikipedia) and retrieving based on semantic similarity to the question.

## Running the Notebook

This solution is designed as a Google Colab notebook (`.ipynb`). Ensure the `train.csv` and `test.csv` files are uploaded to the Colab environment before execution. The notebook will process the `test.csv` file and output a list of predicted answers (A-E).
