# Introduction
VectorFlow is an open source, high throughput, fault tolerant vector embedding pipeline. With a simple API request, you can send raw data that will be embedded and stored in any vector database or returned back to you. 

# How to Run Locally
The api and the worker each have their own virtual environments which you must set up - `python -m venv venv  `. Install the `requirements.txt` for each app into the venv. 

The api can be run locally with `python app.py` in the `api` directory. You must set the following environment variable:
```
INTERNAL_API_KEY
```

The worker can be run with `python worker/worker.py` in the `src` directory. You must set the follow environment variables:
```
INTERNAL_API_KEY
API_REQUEST_URL
```
Plus any 3rd party API keys such as:
```
OPEN_AI_KEY
PINECONE_KEY
```

## Docker
You must have Docker installed locally. First create a `docker network` for the images to run on so the workers can communicate with the api. 
```
docker network create vectorflow
```

Then build the docker images with these commands:
```
docker build -t vectorflow_api:latest .
docker build -file worker/Dockerfile -t vectorflow_worker:latest . 
```
Create an `.env` file with the environment variables - you will pass this file into the containers when they run. Change the `API_REQUEST_URL` to use the name of the api container, for example:
```
API_REQUEST_URL=http://vectorflow_api:8000
```

Run the containers locally with these commands:
```
docker run -d -p 8000:8000 --network vectorflow --name=vectorflow_api --env-file=/Users/davidgarnitz/Documents/Programming/vectorflow/scripts/env_vars.env vectorflow_api:latest 

docker run --network=vectorflow --name=vectorflow_worker -d --env-file=/Users/davidgarnitz/Documents/Programming/vectorflow/scripts/env_vars.env vectorflow_worker:latest
```

# How to Deploy
We recommend deploying using the Docker images available in our [public repo.](https://hub.docker.com/repository/docker/dgarnitz/vectorflow/general). The api runs with `gunicorn` and needs a port open to accept active connections. Your 

# How to Test
Api tests are run from the `api` directory (`vectorflow/src/api`) and can be run with:
```
python -m unittest tests.test_app
python -m unittest tests.test_app.TestApp.test_method_you_wish_to_test
```

Worker tests are run from the `src` directory with the `worker` prefix, for example:
```
python -m unittest src.worker.tests.test_worker
```

# How to Contribute
We love feedback from the community. If you have an idea of how to make this project better, we encourage you to open an issue. Please tag `dgarnitz` and `danmeier2`, and we can discuss how best to approach it.

Our roadmap is outlined in the section below and we would love help in building it out. We recommend you open an issue with a proposed approach in mind before submitted a PR.

Please tag `dgarnitz` on all PRs. 

# Roadmap
- [ ] Connectors to other vector databases such as Weaviate, Milvus, Chroma, Deeplake and Vespa
- [ ] Support for more files types such as `csv`, `word`, `xls`, etc
- [ ] Support for multi-file, directory data ingestion from sources such as S3, Google docs, etc
- [ ] SQL support with SQL ALchemy & Alembic for persistent storage of job & batch information
- [ ] Turn shared objects in Api into package
- [ ] Add stand alone Queue, like SQS or RabbitMQ, with interface to make it technology agnostic
- [ ] Retry mechanism
- [ ] DLQ mechanism
- [ ] Cron job to detect failed and hanging jobs
- [ ] Support for key & secret management
- [ ] Langchain & Llama Index integrations
- [ ] Support for object metadata store and application logic