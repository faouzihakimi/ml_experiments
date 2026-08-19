# Open WebUI with LiteLLM and Ollama

## Overview
This Docker Compose project deploys a local AI chat platform using Open WebUI as the frontend, LiteLLM as the API proxy, and Ollama for local model inference. PostgreSQL is used for LiteLLM's database.

## Greetings
- [Ollama](https://ollama.ai)
- [Open WebUI](https://github.com/open-webui/open-webui)
- [LiteLLM](https://docs.litellm.ai/)

The following repo is adapted from this [article](https://medium.com/codex/self-hosted-ai-chat-interface-with-open-webui-litellm-0a5dcd01381a).

## Services

- **open-webui**: Web interface for interacting with AI models. Accessible at `http://localhost:3000`.
- **litellm**: Proxy server for managing API calls to various LLM providers. Runs on `127.0.0.1:4000`.
- **litellm-db**: PostgreSQL database for LiteLLM.
- **ollama**: Local LLM runtime for running open-source models.

## Setup

1. Create a `.env` file in the same directory as the `docker-compose.yml` and define the required environment variables:
   ```
   WEBUI_SECRET_KEY=
   OPENWEBUI_LITELLM_KEY=
   LITELLM_MASTER_KEY=
   POSTGRES_PASSWORD=
   OPENAI_API_KEY=
   ANTHROPIC_API_KEY=
   MISTRAL_API_KEY=
   ```

2. Ensure `litellm-config.yaml` is present in the same directory.

3. Run:
   ```bash
   docker-compose up -d
   ```

4. Configure littellm (http://localhost:4000/ui) and OnpenWebUI (http://localhost:3000/)

Read the original [article](https://medium.com/codex/self-hosted-ai-chat-interface-with-open-webui-litellm-0a5dcd01381a) for more details).

## Configuration

- Open WebUI connects to LiteLLM at `http://litellm:4000/v1`.
- LiteLLM uses the configuration from `litellm-config.yaml`.
- All services communicate over the `ai-private` network.

### LiteLLM Configuration
The `litellm-config.yaml` defines the following models:
- **ministral**: `ollama/ministral-3:3b` (accessible via `http://ollama:11434`)
- **gemma4**: `ollama/gemma4:latest` (accessible via `http://ollama:11434`)

Fallbacks are configured so that requests for **gemma4** fall back to **ministral** if unavailable.

Any new model can be added on the config file : 

  - model_name: new model
    litellm_params:
      model: provider/model-name
      api_key: "os.environ/MODEL_KEY" #ensure that you have the MODEL_KEY in your environment variables


Additional settings:
- Master key and database URL are loaded from environment variables.
- Models are stored in the database (`store_model_in_db: true`).
- Unused parameters are dropped (`drop_params: true`).

## Data Persistence
- Open WebUI data: `open-webui-data` volume.
- LiteLLM database: `litellm-db-data` volume.
- Ollama models: `ollama-data` volume.