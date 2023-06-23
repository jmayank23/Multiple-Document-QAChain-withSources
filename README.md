# Multiple Document QA Chain with Sources

This code provides a question-answering (QA) system using Langchain, which allows you to chat with multiple documents (PDF, TXT, etc.) as sources. The code also includes support for creating a simple Streamlit app for a user-friendly interface.

<img width="622" alt="Screen Shot 2023-06-23 at 1 25 40 PM" src="https://github.com/jmayank23/Multiple-Document-QAChain-withSources/assets/27727185/f2e9edff-09a3-4bfd-9a5f-a2b3516cf415">

**Note: The conversation shown in the screenshot was based on a PDF by UCSD International Student Office. The information displayed in the chat should not be taken as truth; it is for demonstration purposes only.**


## Unique Features

This repository includes the following unique features:

1. **Persistent Database**: The code provides an option for the database to persist between sessions. By specifying a directory for `persist_directory` when creating the database, you can avoid recreating the index each time the code runs. To create a persistent database, use the following code:
   ```python
   vectordb = Chroma.from_documents(documents, embedding=embedding, persist_directory='db')
   ```

2. **Customizable Prompts**: The code clearly demonstrates how the prompts are sent to the language model (LLM) under the hood. This allows you to easily understand and modify the prompts to tailor the responses for your specific use case. You can explore and adjust the prompts in the following code section:
   ```python
   # Print the chat prompts
   print(qa_chain.combine_documents_chain.llm_chain.prompt.messages[0].prompt.template)
   print(qa_chain.combine_documents_chain.llm_chain.prompt.messages[1].prompt.template)
   ```

## Code description

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
   - To create a new database each time:
   ```python
   embedding = OpenAIEmbeddings()
   
   vectordb = Chroma.from_documents(documents, embedding=embedding, persist_directory=None)
   ```
   - To create a database that persists between sessions:
   ```python
   embedding = OpenAIEmbeddings()
   
   vectordb = Chroma.from_documents(documents, embedding=embedding, persist_directory='db')
   ```

3. Create the question-answering chain:
   ```python
   turbo_llm = ChatOpenAI(temperature=0, model_name='gpt-3.5-turbo')
   retriever = vectordb.as_retriever(search_kwargs={"k": 3})
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
