# Project Structure

├── Finance/  
│   ├── AMEX_ATRS_2012.pdf  
│   ├── spice.pdf  
├── app/  
│   ├── celery/  
│   │   ├── __init__.py  
│   │   ├── worker.py  
│   ├── chat/  
│   │   ├── embeddings/  
│   │   │   ├── __init__.py  
│   │   │   ├── openai.py  
│   │   ├── llms/  
│   │   │   ├── __init__.py  
│   │   │   ├── chatopenai.py  
│   │   ├── memories/  
│   │   │   ├── __init__.py  
│   │   │   ├── sql_memory.py  
│   │   ├── models/  
│   │   │   ├── __init__.py  
│   │   ├── vector_stores/  
│   │   │   ├── __init__.py  
│   │   │   ├── pinecone.py  
│   │   ├── __init__.py  
│   │   ├── chat.py  
│   │   ├── create_embeddings.py  
│   │   ├── score.py  
│   ├── web/  
│   │   ├── config/  
│   │   │   ├── __init__.py  
│   │   ├── db/  
│   │   │   ├── models/  
│   │   │   │   ├── __init__.py  
│   │   │   │   ├── base.py  
│   │   │   │   ├── conversation.py  
│   │   │   │   ├── message.py   
│   │   │   │   ├── pdf.py  
│   │   │   │   ├── user.py   
│   │   │   ├── __init__.py   
│   │   ├── tasks/   
│   │   │   ├── __init__.py   
│   │   │   ├── embeddings.py   
│   │   ├── views/   
│   │   │   ├── __init__.py   
│   │   │   ├── auth_views.py   
│   │   │   ├── client_views.py  
│   │   │   ├── conversation_views.py    
│   │   │   ├── pdf_views.py  
│   │   │   ├── score_views.py  
│   │   ├── __init__.py  
│   │   ├── api.py  
│   │   ├── files.py  
│   │   ├── hooks.py  
├── client/  
│   ├── build/  
│   ├── src/  
│   ├── static/  
├── Pipfile   
├── Pipfile.lock  
├── README.md  
├── requirements.txt  
├── tasks.py  
├── tomarkdown.py  

# Explanation

There are 2 main folders: "app" and "client".  
The "client" folder corresponds to the webpage formatting. This includes all the HTML, javascript and styling that shows up in the browser. This was created using a conventional chatting interface from different repos as reference.  
The "app" folder contains all the heavy lifting in terms of orchestrating between langchain, workers, openai's llm and databases. 

## Overview of the "app" Folder
The `app` folder encapsulates the core functionality of a web application designed for uploading PDF documents, processing them to create embeddings using OpenAI's models, storing these embeddings in Pinecone, and enabling users to perform Retrieval-Augmented Generation (RAG) tasks by querying the embedded data through a conversational interface.

### Step-by-Step Explanation

#### 1. Receiving and Processing PDFs
- **PDF Upload and Storage**: Users upload PDF files through a web interface handled by Flask routes defined in [`app/web/views/pdf_views.py`](./app/web/views/pdf_views.py). The files are managed by [`app/web/files.py`](./app/web/files.py), which takes care of storing the files and handling downloads/uploads.
- **Creating Embeddings**: Once a PDF is uploaded, embeddings are generated for the content of the PDF. This process is implemented in [`app/chat/create_embeddings.py`](./app/chat/create_embeddings.py). The file uses OpenAI's models to convert text extracted from PDFs into embeddings, which are vector representations of the document content.

#### 2. Managing Embeddings with Pinecone
- **Storing Embeddings**: The embeddings generated from the PDFs are stored in Pinecone, a vector database optimized for fast retrieval of high-dimensional data. The interaction with Pinecone is handled through [`app/chat/vector_stores/pinecone.py`](./app/chat/vector_stores/pinecone.py), where embeddings are added to a Pinecone index.
- **Retrieving Embeddings**: When users query the system, Pinecone is used to find the most relevant embeddings that match the query, facilitating efficient and relevant responses.

#### 3. Handling Queries with Langchain and LLM
- **Query Processing**: Users can query the system regarding the content of the uploaded PDFs. This querying is handled by language models and retrieval systems configured in [`app/chat/llms/chatopenai.py`](./app/chat/llms/chatopenai.py) and using Langchain to orchestrate the interaction.
- **Langchain Integration**: Langchain is a framework that combines machine learning models (LLMs) with retrieval systems (like Pinecone) to perform complex tasks such as answering questions based on the content in the embeddings. The setup for Langchain is found in [`app/chat/chat.py`](./app/chat/chat.py), which configures how the queries are processed and responded to.

#### 4. Additional Complexities and System Components
- **Redis and Celery**: For handling asynchronous tasks, such as processing PDF uploads and generating embeddings, the application uses Celery with Redis as the message broker. Celery manages background tasks to keep the application responsive, while Redis queues these tasks efficiently. This setup is configured in [`app/celery/__init__.py`](./app/celery/__init__.py) and [`app/web/tasks/embeddings.py`](./app/web/tasks/embeddings.py) for task definitions.
- **SQL Database Management**: The application uses an SQL database to manage user data and metadata about PDFs and conversations. Models and database interactions are defined in [`app/web/db/models/`](./app/web/db/models/) (e.g., `user.py`, `conversation.py`, `message.py`). This SQL database helps maintain a persistent state and user sessions.
- **Flask Application Setup**: The Flask application setup, including route definitions, Celery integration, and other configurations, is primarily managed in [`app/web/__init__.py`](./app/web/__init__.py).

#### 5. Linking It All Together
- **User Interface and Interaction**: The front end, found in the `client` folder outside of `app`, interacts with these backend functionalities through APIs defined in the Flask app, particularly under [`app/web/views/`](./app/web/views/). These APIs handle user requests for uploading PDFs, submitting queries, and receiving responses.

### File Reference Guide
For more details or specific implementations, refer to the following files:
- **PDF Management**: [`app/web/views/pdf_views.py`](./app/web/views/pdf_views.py) and [`app/web/files.py`](./app/web/files.py)
- **Embedding Generation**: [`app/chat/create_embeddings.py`](./app/chat/create_embeddings.py)
- **Pinecone Integration**: [`app/chat/vector_stores/pinecone.py`](./app/chat/vector_stores/pinecone.py)
- **Langchain and LLM Setup**: [`app/chat/chat.py`](./app/chat/chat.py) and [`app/chat/llms/chatopenai.py`](./app/chat/llms/chatopenai.py)
- **Asynchronous Task Management**: [`app/celery/__init__.py`](./app/celery/__init__.py) and [`app/web/tasks/embeddings.py`](./app/web/tasks/embeddings.py)
- **Database Models and Interactions**: [`app/web/db/models/`](./app/web/db/models/)
- **Flask Setup and Routing**: [`app/web/__init__.py`](./app/web/__init__.py)

# First Time Setup

## Using Pipenv 

```
# Install dependencies
pipenv install

# Create a virtual environment
pipenv shell

# Initialize the database
flask --app app.web init-db

```

## Requirements file has been added so Venv can also be used


# Running the app [Pipenv]

There are three separate processes that need to be running for the app to work: the server, the worker, and Redis.

If you stop any of these processes, you will need to start them back up!

Commands to start each are listed below. If you need to stop them, select the terminal window the process is running in and press Control-C

### To run the Python server

Open a new terminal window and create a new virtual environment:

```
pipenv shell
```

Then:

```
inv dev
```

### To run the worker

Open a new terminal window and create a new virtual environment:

```
pipenv shell
```

Then:

```
inv devworker
```

### To run Redis (IOS or Linux)

```
redis-server
```

### In case of Windows (my case), Redis URI key is added to a .env file

### To reset the database

Open a new terminal window and create a new virtual environment:

```
pipenv shell
```

Then:

```
flask --app app.web init-db
```

# Running the app [Venv]

Basically same commands but open a new window and create a Venv and use the same commands.

# .env file

.env file should contain the following:

SECRET_KEY=123  
SQLALCHEMY_DATABASE_URI=sqlite:///sqlite.db
UPLOAD_URL=https://prod-upload-langchain.fly.dev  

OPENAI_API_KEY=  

REDIS_URI=  

PINECONE_API_KEY=721801c1-74d8-4f22-b861-2eca118f8792  
PINECONE_ENV_NAME=us-east-1   
PINECONE_INDEX_NAME=docs  
