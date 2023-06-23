# Multiple Document QA Chain withS ources

This code provides a question-answering (QA) system using Langchain, which allows you to chat with multiple documents (PDF, TXT, etc.) as sources. The code also includes support for creating a simple Streamlit app for a user-friendly interface.

## Installation

To install the required packages, run the following command:

```shell
pip install langchain openai tiktoken chromadb pypdf streamlit
```

## Code description

### Setup OpenAI API Key

Set your OpenAI API key as an environment variable. Replace `KEY` with your actual API key.

```python
import os

os.environ["OPENAI_API_KEY"] = "KEY"
```

### Usage

1. Load and process the documents:
   - If you have text files, use the `TextLoader` class. Update the file path in the `DirectoryLoader` constructor to the directory containing your text files.
   ```python
   loader = DirectoryLoader('/path/to/text/files/', glob="./*.txt", loader_cls=TextLoader)
   ```
   - If you have PDF files, use the `PyPDFLoader` class. Update the file path in the `DirectoryLoader` constructor to the directory containing your PDF files.
   ```python
   loader = DirectoryLoader('/path/to/pdf/files/', glob="./*.pdf", loader_cls=PyPDFLoader)
   ```

2. Create the document database:
   ```python
   # Embed and store the texts
   embedding = OpenAIEmbeddings()
   
   vectordb = Chroma.from_documents(documents, embedding=embedding, persist_directory='db')
   ```

3. Create the question-answering chain:
   ```python
   # Set up the turbo LLM
   turbo_llm = ChatOpenAI(temperature=0, model_name='gpt-3.5-turbo')
   
   # Create the retriever for the database
   retriever = vectordb.as_retriever(search_kwargs={"k": 3})
   
   # Create the question-answering chain
   qa_chain = RetrievalQA.from_chain_type(llm=turbo_llm, chain_type="stuff", retriever=retriever, return_source_documents=True)
   ```

4. Use the chat prompts to interact with the QA system:
   ```python
   # Print the chat prompts
   print(qa_chain.combine_documents_chain.llm_chain.prompt.messages[0].prompt.template)
   print(qa_chain.combine_documents_chain.llm_chain.prompt.messages[1].prompt.template)
   
   # Main loop for user input
   while True:
       query = input("Enter your query (or 'q' to quit): ")
       if query == 'q':
           break
       llm_response = qa_chain(query)
       process_llm_response(llm_response)
   ```

5. Run the code:
   ```shell
   python your_script.py
   ```

### Streamlit App

The code also includes a simple Streamlit app for a more interactive experience. To run the Streamlit app, follow these steps:

1. Uncomment the necessary code lines in the provided script.

2. Run the Streamlit app:
   ```shell
   streamlit run app.py
   ```

3. Access the app in your browser by clicking on the external URL provided.
