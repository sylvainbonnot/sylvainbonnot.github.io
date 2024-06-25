---
title:  LLM with llama2 locally with retriever

publishDate: 04 Mar 2024
description: LLM, locally
---



Based on the Langchain's docs, I want to use the **llama2** model **locally**, while augmenting the search by providing the content of a given web page.

So we start with some installs: 

```console
pip install langchain
```

```console
pip install langchain-openai
```


# Getting the actual model
First one needs to install ```ollama``` (by downloading the application), 
pull the model ```llama2``` that we will use:
```console
ollama pull llama2
```

and start the server 
```console
ollama serve
```

Notice that if you decided to run in the console the model by typing ```ollama run llama2``` the server is already started. 

```python
from langchain_community.llms import Ollama
llm = Ollama(model="llama2")
```

# Making a retriever

```console
pip install beautifulsoup4
```

We point the model to a particular web page. 
```python
from langchain_community.document_loaders import WebBaseLoader
loader = WebBaseLoader("https://en.wikipedia.org/wiki/Brunoy")

docs = loader.load()
```


## Create the embeddings
```python
from langchain_community.embeddings import OllamaEmbeddings

embeddings = OllamaEmbeddings()
```

```console
pip install faiss-cpu
```


```python
from langchain_community.vectorstores import FAISS
from langchain_text_splitters import RecursiveCharacterTextSplitter


text_splitter = RecursiveCharacterTextSplitter()
documents = text_splitter.split_documents(docs)
vector = FAISS.from_documents(documents, embeddings)
```


```python
from langchain.chains.combine_documents import create_stuff_documents_chain

prompt = ChatPromptTemplate.from_template("""Answer the following question based only on the provided context:

<context>
{context}
</context>

Question: {input}""")

document_chain = create_stuff_documents_chain(llm, prompt)
```

```python
from langchain.chains import create_retrieval_chain

retriever = vector.as_retriever()
retrieval_chain = create_retrieval_chain(retriever, document_chain)

```

We are now completely done with the setup and we can start asking questions: 

```python
response = retrieval_chain.invoke({"input": "How far is Brunoy from Paris?"})
print(response["answer"])
>>> Brunoy is located approximately 30 kilometers (18.6 miles) southwest of Paris, France. The exact distance depends on the specific location within Paris and Brunoy, but as a rough estimate, it takes about 30-40 minutes to drive from central Paris to Brunoy via the A10 highway. There are also public transportation options available, such as buses and trains, which can take slightly longer but offer a convenient alternative to driving.
```

And we are done!
