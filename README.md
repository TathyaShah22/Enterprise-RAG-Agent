# LangGraph RAG Research Agent Template Project

[![CI](https://github.com/langchain-ai/rag-research-agent-template/actions/workflows/unit-tests.yml/badge.svg)](https://github.com/langchain-ai/rag-research-agent-template/actions/workflows/unit-tests.yml)
[![Integration Tests](https://github.com/langchain-ai/rag-research-agent-template/actions/workflows/integration-tests.yml/badge.svg)](https://github.com/langchain-ai/rag-research-agent-template/actions/workflows/integration-tests.yml)

[![Open in - LangGraph Studio](https://img.shields.io/badge/Open_in-LangGraph_Studio-00324d.svg?logo=data:image/svg%2bxml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSI4NS4zMzMiIGhlaWdodD0iODUuMzMzIiB2ZXJzaW9uPSIxLjAiIHZpZXdCb3g9IjAgMCA2NCA2NCI+PHBhdGggZD0iTTEzIDcuOGMtNi4zIDMuMS03LjEgNi4zLTYuOCAyNS43LjQgMjQuNi4zIDI0LjUgMjUuOSAyNC41QzU3LjUgNTggNTggNTcuNSA1OCAzMi4zIDU4IDcuMyA1Ni43IDYgMzIgNmMtMTIuOCAwLTE2LjEuMy0xOSAxLjhtMzcuNiAxNi42YzIuOCAyLjggMy40IDQuMiAzLjQgNy42cy0uNiA0LjgtMy40IDcuNkw0Ny4yIDQzSDE2LjhsLTMuNC0zLjRjLTQuOC00LjgtNC44LTEwLjQgMC0xNS4ybDMuNC0zLjRoMzAuNHoiLz48cGF0aCBkPSJNMTguOSAyNS42Yy0xLjEgMS4zLTEgMS43LjQgMi41LjkuNiAxLjcgMS44IDEuNyAyLjcgMCAxIC43IDIuOCAxLjYgNC4xIDEuNCAxLjkgMS40IDIuNS4zIDMuMi0xIC42LS42LjkgMS40LjkgMS41IDAgMi43LS41IDIuNy0xIDAtLjYgMS4xLS44IDIuNi0uNGwyLjYuNy0xLjgtMi45Yy01LjktOS4zLTkuNC0xMi4zLTExLjUtOS44TTM5IDI2YzAgMS4xLS45IDIuNS0yIDMuMi0yLjQgMS41LTIuNiAzLjQtLjUgNC4yLjguMyAyIDEuNyAyLjUgMy4xLjYgMS41IDEuNCAyLjMgMiAyIDEuNS0uOSAxLjItMy41LS40LTMuNS0yLjEgMC0yLjgtMi44LS44LTMuMyAxLjYtLjQgMS42LS41IDAtLjYtMS4xLS4xLTEuNS0uNi0xLjItMS42LjctMS43IDMuMy0yLjEgMy41LS41LjEuNS4yIDEuNi4zIDIuMiAwIC43LjkgMS40IDEuOSAxLjYgMi4xLjQgMi4zLTIuMy4yLTMuMi0uOC0uMy0yLTEuNy0yLjUtMy4xLTEuMS0zLTMtMy4zLTMtLjUiLz48L3N2Zz4=)](https://langgraph-studio.vercel.app/templates/open?githubUrl=https://github.com/langchain-ai/rag-research-agent-template)

# Enterprise RAG Research Agent

Welcome to my Enterprise RAG Agent project! I built this repository as a hands-on way to deeply understand and implement Retrieval-Augmented Generation (RAG) architectures. By leveraging [LangGraph](https://github.com/langchain-ai/langgraph) and [LangGraph Studio](https://github.com/langchain-ai/langgraph-studio), I created a stateful, multi-actor application that can ingest documents, manage chat history, and conduct multi-step research to answer complex queries.

![Graph view in LangGraph studio UI](./static/studio_ui.png)

## What it does

This project operates using three primary graphs:

* An "index" graph (`src/index_graph/graph.py`)
* A "retrieval" graph (`src/retrieval_graph/graph.py`)
* A "researcher" subgraph, which is part of the retrieval graph (`src/retrieval_graph/researcher_graph/graph.py`)

The index graph takes in document objects and indexes them.

```json
[{ "page_content": "LangGraph is a library for building stateful, multi-actor applications with LLMs, used to create agent and multi-agent workflows." }]
```

If an empty list is provided by default, a list of sample documents from `src/sample_docs.json` is indexed instead. Those sample documents are based on the conceptual guides for LangChain and LangGraph.

The retrieval graph manages a chat history and responds based on the fetched documents. Specifically, it executes the following flow:

1. Takes a user query as input.
2. Analyzes the query and determines how to route it. If the query is about "LangChain", it creates a research plan based on the user's query and passes the plan to the researcher subgraph. If the query is ambiguous, it asks for more information. If the query is general and unrelated to LangChain, it lets the user know.
3. If routed to the researcher subgraph, it runs for each step in the research plan until no steps are left. It first generates a list of queries based on the step, then retrieves the relevant documents in parallel for all queries, and returns the documents to the retrieval graph.
4. Finally, the retrieval graph generates a response based on the retrieved documents and the conversation context.

## Getting Started

Assuming you have already installed LangGraph Studio, here is how to set up the environment:

1. Create a `.env` file using the terminal command: `cp .env.example .env`
2. Select your retriever and index, and save the access instructions to your `.env` file.

### Setup Retriever

The default value for `retriever_provider` is shown below:

```yaml
retriever_provider: elastic-local
```

Follow the instructions below to get set up, or pick one of the additional options.

#### Elasticsearch

Elasticsearch is an open source distributed search and analytics engine, scalable data store and vector database optimized for speed and relevance on production workloads. It can be configured as the knowledge base provider for this agent by being deployed on Elastic Cloud or on your local environment.

**Elasticsearch Serverless**

1. Signup for a free 14 day trial with Elasticsearch Serverless.
2. Get the Elasticsearch URL, found on home under "Copy your connection details".
3. Create an API key found on home under "API Key".
4. Copy the URL and API key to your `.env` file created above:

```
ELASTICSEARCH_URL=<ES_URL>
ELASTICSEARCH_API_KEY=<API_KEY>
```

**Elastic Cloud**

1. Signup for a free 14 day trial with Elastic Cloud.
2. Get the Elasticsearch URL, found under Applications of your deployment.
3. Create an API key. See the official elastic documentation for more information.
4. Copy the URL and API key to your `.env` file created above:

```
ELASTICSEARCH_URL=<ES_URL>
ELASTICSEARCH_API_KEY=<API_KEY>
```

**Local Elasticsearch (Docker)**

```bash
docker run \
  -p 127.0.0.1:9200:9200 \
  -d \
  --name elasticsearch \
  -e ELASTIC_PASSWORD=changeme \
  -e "discovery.type=single-node" \
  -e "xpack.security.http.ssl.enabled=false" \
  -e "xpack.license.self_generated.type=trial" \
  docker.elastic.co/elasticsearch/elasticsearch:8.15.1
```

See the official Elastic documentation for more information on running it locally.

Then populate the following in your `.env` file (since both Elasticsearch and LangGraph Studio run in Docker, we use `host.docker.internal`):

```
ELASTICSEARCH_URL=http://host.docker.internal:9200
ELASTICSEARCH_USER=elastic
ELASTICSEARCH_PASSWORD=changeme
```

#### MongoDB Atlas

MongoDB Atlas is a fully managed cloud database that includes vector search capabilities for AI applications.

1. Go to the MongoDB Atlas website and sign up for a free account to create a free cluster.
2. Follow the instructions at the Mongo docs to create a vector search index.
3. Use the collection `langgraph_retrieval_agent.default` and create the index there.
4. Add an indexed filter for path `user_id`. **IMPORTANT:** select Atlas Vector Search NOT Atlas Search when creating the index.

Your final JSON editor configuration should look something like the following (the exact `numDimensions` may differ based on the embedding model):

```json
{
  "fields": [
    {
      "numDimensions": 1536,
      "path": "embedding",
      "similarity": "cosine",
      "type": "vector"
    }
  ]
}
```

5. In the Atlas dashboard, click on "Connect" for your cluster and choose "Connect your application" to copy the provided connection string.
6. Add your MongoDB Atlas connection string to the `.env` file, replacing the placeholder credentials with your actual cluster information:

```
MONGODB_URI="mongodb+srv://username:password@your-cluster-url.mongodb.net/?retryWrites=true&w=majority&appName=your-cluster-name"
```

#### Pinecone Serverless

Pinecone is a managed, cloud native vector database that provides long term memory for high performance AI applications.

1. Sign up for a Pinecone account at https://login.pinecone.io/login.
2. Generate an API key from the Pinecone console.
3. Create a Serverless index, select "cosine" as the metric, and set the dimension based on your embedding model (e.g., 1536 for OpenAI embeddings).
4. Add your newly created index and API key to your `.env` file:

```
PINECONE_API_KEY=your-api-key
PINECONE_INDEX_NAME=your-index-name
```

### Setup Model

The default values for `response_model` and `query_model` are shown below:

```yaml
response_model: anthropic/claude-3-5-sonnet-20240620
query_model: anthropic/claude-3-haiku-20240307
```

Follow the instructions below to get set up, or pick one of the additional options.

#### Anthropic

To use Anthropic's chat models:

1. Sign up for an Anthropic API key.
2. Add the API key to your `.env` file:

```
ANTHROPIC_API_KEY=your-api-key
```

#### OpenAI

To use OpenAI's chat models:

1. Sign up for an OpenAI API key.
2. Add the API key to your `.env` file:

```
OPENAI_API_KEY=your-api-key
```

### Setup Embedding Model

The default value for `embedding_model` is shown below:

```yaml
embedding_model: openai/text-embedding-3-small
```

Follow the instructions below to get set up, or pick one of the additional options.

#### OpenAI

To use OpenAI's embeddings:

1. Sign up for an OpenAI API key.
2. Add the API key to your `.env` file:

```
OPENAI_API_KEY=your-api-key
```

#### Cohere

To use Cohere's embeddings:

1. Sign up for a Cohere API key.
2. Add the API key to your `.env` file:

```bash
COHERE_API_KEY=your-api-key
```

## Using the Agent

Once you've set up your retriever and saved your model secrets, it's time to try it out! First, we need to add some information to the index. Open studio, select the "indexer" graph from the dropdown in the top left, and then add some content to chat over. You can invoke it with an empty list to index the default sample documents from LangChain and LangGraph documentation.

You'll know that the indexing is complete when the indexer "deletes" the content from its graph memory, meaning it has been persisted in your configured storage provider.

Next, open the "retrieval_graph" using the dropdown in the top left. Ask it questions about LangChain to confirm it can fetch the required information!

## How to Customize

I designed this retrieval agent so it can be easily adapted for other use cases:

- **Change the retriever:** Switch between different vector stores (Elasticsearch, MongoDB, Pinecone) by modifying the `retriever_provider` in the configuration.
- **Modify the embedding model:** Change the embedding model used for document indexing and query embedding by updating the `embedding_model` in the configuration. Options include various OpenAI and Cohere models.
- **Adjust search parameters:** Fine tune the retrieval process by modifying the `search_kwargs` in the configuration to control aspects like the number of documents retrieved or similarity thresholds.
- **Customize the response generation:** Modify the `response_system_prompt` to change how the agent formulates its responses, adjusting its personality or adding specific instructions for answer generation.
- **Modify prompts:** Update the prompts used for user query routing, research planning, and query generation in `src/retrieval_graph/prompts.py` to better suit your specific use case. You can easily tweak the `research_plan_system_prompt` or the `generate_queries_system_prompt` directly in LangGraph Studio.
- **Change the language model:** Update the `response_model` in the configuration to use different language models for response generation, like Claude models from Anthropic or other providers.
- **Extend the graph:** Add new nodes or modify existing ones in the `src/retrieval_graph/graph.py` file to introduce additional processing steps or decision points in the agent's workflow.
- **Add tools:** Implement extra tools to expand the researcher agent's capabilities beyond simple retrieval generation.

## Development

While iterating on the graph, you can edit past state and rerun the app from past states to debug specific nodes. Local changes will be automatically applied via hot reload. Try adding an interrupt before the agent calls the researcher subgraph, updating the default system message in `src/retrieval_graph/prompts.py` to take on a persona, or adding additional nodes and edges!

Follow up requests will be appended to the same thread. You can create an entirely new thread, clearing previous history, using the `+` button in the top right.

<!--
Configuration auto-generated by `langgraph template lock`. DO NOT EDIT MANUALLY.
{
  "config_schemas": {
    "indexer": {
      "type": "object",
      "properties": {
        "embedding_model": {
          "type": "string",
          "default": "openai/text-embedding-3-small",
          "description": "Name of the embedding model to use. Must be a valid embedding model name.",
          "environment": [
            {
              "value": "cohere/embed-english-light-v2.0",
              "variables": "COHERE_API_KEY"
            },
            {
              "value": "cohere/embed-english-light-v3.0",
              "variables": "COHERE_API_KEY"
            },
            {
              "value": "cohere/embed-english-v2.0",
              "variables": "COHERE_API_KEY"
            },
            {
              "value": "cohere/embed-english-v3.0",
              "variables": "COHERE_API_KEY"
            },
            {
              "value": "cohere/embed-multilingual-light-v3.0",
              "variables": "COHERE_API_KEY"
            },
            {
              "value": "cohere/embed-multilingual-v2.0",
              "variables": "COHERE_API_KEY"
            },
            {
              "value": "cohere/embed-multilingual-v3.0",
              "variables": "COHERE_API_KEY"
            },
            {
              "value": "openai/text-embedding-3-large",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/text-embedding-3-small",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/text-embedding-ada-002",
              "variables": "OPENAI_API_KEY"
            }
          ]
        },
        "retriever_provider": {
          "enum": [
            "elastic-local",
            "elastic",
            "mongodb",
            "pinecone"
          ],
          "default": "elastic-local",
          "description": "The vector store provider to use for retrieval. Options are 'elastic', 'pinecone', or 'mongodb'.",
          "environment": [
            {
              "value": "elastic",
              "variables": [
                "ELASTICSEARCH_URL",
                "ELASTICSEARCH_API_KEY"
              ]
            },
            {
              "value": "elastic-local",
              "variables": [
                "ELASTICSEARCH_URL",
                "ELASTICSEARCH_USER",
                "ELASTICSEARCH_PASSWORD"
              ]
            },
            {
              "value": "mongodb",
              "variables": [
                "MONGODB_URI"
              ]
            },
            {
              "value": "pinecone",
              "variables": [
                "PINECONE_API_KEY",
                "PINECONE_INDEX_NAME"
              ]
            }
          ],
          "type": "string"
        }
      }
    },
    "retrieval_graph": {
      "type": "object",
      "properties": {
        "embedding_model": {
          "type": "string",
          "default": "openai/text-embedding-3-small",
          "description": "Name of the embedding model to use. Must be a valid embedding model name.",
          "environment": [
            {
              "value": "cohere/embed-english-light-v2.0",
              "variables": "COHERE_API_KEY"
            },
            {
              "value": "cohere/embed-english-light-v3.0",
              "variables": "COHERE_API_KEY"
            },
            {
              "value": "cohere/embed-english-v2.0",
              "variables": "COHERE_API_KEY"
            },
            {
              "value": "cohere/embed-english-v3.0",
              "variables": "COHERE_API_KEY"
            },
            {
              "value": "cohere/embed-multilingual-light-v3.0",
              "variables": "COHERE_API_KEY"
            },
            {
              "value": "cohere/embed-multilingual-v2.0",
              "variables": "COHERE_API_KEY"
            },
            {
              "value": "cohere/embed-multilingual-v3.0",
              "variables": "COHERE_API_KEY"
            },
            {
              "value": "openai/text-embedding-3-large",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/text-embedding-3-small",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/text-embedding-ada-002",
              "variables": "OPENAI_API_KEY"
            }
          ]
        },
        "retriever_provider": {
          "enum": [
            "elastic-local",
            "elastic",
            "mongodb",
            "pinecone"
          ],
          "default": "elastic-local",
          "description": "The vector store provider to use for retrieval. Options are 'elastic', 'pinecone', or 'mongodb'.",
          "environment": [
            {
              "value": "elastic",
              "variables": [
                "ELASTICSEARCH_URL",
                "ELASTICSEARCH_API_KEY"
              ]
            },
            {
              "value": "elastic-local",
              "variables": [
                "ELASTICSEARCH_URL",
                "ELASTICSEARCH_USER",
                "ELASTICSEARCH_PASSWORD"
              ]
            },
            {
              "value": "mongodb",
              "variables": [
                "MONGODB_URI"
              ]
            },
            {
              "value": "pinecone",
              "variables": [
                "PINECONE_API_KEY",
                "PINECONE_INDEX_NAME"
              ]
            }
          ],
          "type": "string"
        },
        "response_model": {
          "type": "string",
          "default": "anthropic/claude-3-5-sonnet-20240620",
          "description": "The language model used for generating responses. Should be in the form: provider/model-name.",
          "environment": [
            {
              "value": "anthropic/claude-1.2",
              "variables": "ANTHROPIC_API_KEY"
            },
            {
              "value": "anthropic/claude-2.0",
              "variables": "ANTHROPIC_API_KEY"
            },
            {
              "value": "anthropic/claude-2.1",
              "variables": "ANTHROPIC_API_KEY"
            },
            {
              "value": "anthropic/claude-3-5-sonnet-20240620",
              "variables": "ANTHROPIC_API_KEY"
            },
            {
              "value": "anthropic/claude-3-haiku-20240307",
              "variables": "ANTHROPIC_API_KEY"
            },
            {
              "value": "anthropic/claude-3-opus-20240229",
              "variables": "ANTHROPIC_API_KEY"
            },
            {
              "value": "anthropic/claude-3-sonnet-20240229",
              "variables": "ANTHROPIC_API_KEY"
            },
            {
              "value": "anthropic/claude-instant-1.2",
              "variables": "ANTHROPIC_API_KEY"
            },
            {
              "value": "openai/gpt-3.5-turbo",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-3.5-turbo-0125",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-3.5-turbo-0301",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-3.5-turbo-0613",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-3.5-turbo-1106",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-3.5-turbo-16k",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-3.5-turbo-16k-0613",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4-0125-preview",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4-0314",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4-0613",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4-1106-preview",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4-32k",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4-32k-0314",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4-32k-0613",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4-turbo",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4-turbo-preview",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4-vision-preview",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4o",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4o-mini",
              "variables": "OPENAI_API_KEY"
            }
          ]
        },
        "query_model": {
          "type": "string",
          "default": "anthropic/claude-3-haiku-20240307",
          "description": "The language model used for processing and refining queries. Should be in the form: provider/model-name.",
          "environment": [
            {
              "value": "anthropic/claude-1.2",
              "variables": "ANTHROPIC_API_KEY"
            },
            {
              "value": "anthropic/claude-2.0",
              "variables": "ANTHROPIC_API_KEY"
            },
            {
              "value": "anthropic/claude-2.1",
              "variables": "ANTHROPIC_API_KEY"
            },
            {
              "value": "anthropic/claude-3-5-sonnet-20240620",
              "variables": "ANTHROPIC_API_KEY"
            },
            {
              "value": "anthropic/claude-3-haiku-20240307",
              "variables": "ANTHROPIC_API_KEY"
            },
            {
              "value": "anthropic/claude-3-opus-20240229",
              "variables": "ANTHROPIC_API_KEY"
            },
            {
              "value": "anthropic/claude-3-sonnet-20240229",
              "variables": "ANTHROPIC_API_KEY"
            },
            {
              "value": "anthropic/claude-instant-1.2",
              "variables": "ANTHROPIC_API_KEY"
            },
            {
              "value": "openai/gpt-3.5-turbo",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-3.5-turbo-0125",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-3.5-turbo-0301",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-3.5-turbo-0613",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-3.5-turbo-1106",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-3.5-turbo-16k",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-3.5-turbo-16k-0613",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4-0125-preview",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4-0314",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4-0613",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4-1106-preview",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4-32k",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4-32k-0314",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4-32k-0613",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4-turbo",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4-turbo-preview",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4-vision-preview",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4o",
              "variables": "OPENAI_API_KEY"
            },
            {
              "value": "openai/gpt-4o-mini",
              "variables": "OPENAI_API_KEY"
            }
          ]
        }
      }
    }
  }
}
-->
