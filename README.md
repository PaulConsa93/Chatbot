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
The folder "client" corresponds to the webpage formatting. This includes all the HTML, javascript and styling that shows up in the browser. This was created using a conventional chatting interface from different repos as reference.  
The "app" folder contains all the heavy lifting in terms of orchestrating between langchain, workers, openai's llm and databases. It's central to the application's functionality, organizing the code into modules that manage different aspects of the application. Here's a deeper dive into each component and the tools used:  
* Celery: _celery_ is an asynchronous task queue/job queue based on distributed message passing. It is used to handle operations that are resource-intensive and hence are executed outside of the main flow of the program, such as sending emails, processing files, or executing batch jobs. This allows the web server to respond quickly to web requests without waiting for these operations to complete.  This was added to the project for future scalability (see Redis - message broker and worker.py ).  
*  

* get_messages_by_conversation_id()
* add_messages_to_conversation()
* get_conversation_components()
* set_conversation_components()
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
