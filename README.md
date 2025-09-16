# Repository sage-code-assist-k8s

The sage-code-assist-k8s repository contains a [Helm Chart](https://helm.sh/) which builds in the CIRRUS cloud an [Ollama](https://ollama.com/) backend to support AI code assistance (aka "vibe coding"). The deployment of this infrastructure creates two containers: Ollama itself and an NGINX reverse proxy. The Ollama container has a large persistent volume attached to it to provide disk space for multiple AI models and uses an NVIDIA GPU. The NGINX container implements token based access to the Ollama container. Only users bearing a permitted token are authorized to access Ollama.    

## Curl Based Usage

### Assure Ollama is running:
```
curl --header "Authorization: Bearer phony-key1" https://sage.code.assist.k8s.ucar.edu
```

### Download a model:
```
curl --header "Authorization: Bearer phony-key1" https://sage.code.assist.k8s.ucar.edu/api/pull -d '{
  "model": "llama3.2:3b"
}'
```

### Show all locally installed models:
```
curl --header "Authorization: Bearer phony-key1" https://sage.code.assist.k8s.ucar.edu/api/tags
```

### Show information about a model:
```
curl --header "Authorization: Bearer phony-key1" https://sage.code.assist.k8s.ucar.edu/api/show -d '{
  "model": "llama3.2:3b"
}'
```

### Delete a model:
```
curl --header "Authorization: Bearer phony-key1" -X DELETE https://sage.code.assist.k8s.ucar.edu/api/delete -d '{
  "model": "llama3.2:3b"
}'
```

### Test a model:
```
curl --header "Authorization: Bearer phony-key1" --header "Content-Type: application/json" -X POST https://sage.code.assist.k8s.ucar.edu/v1/chat/completions -d '{
  "model": "llama3.2:3b",
  "messages": [
    {
      "role": "user",
      "content": "What is the capital of Spain?"
    }
  ]
}'
```

## Intellij Plugin Continue

Install the Intellij plugin [Continue](https://www.continue.dev/). This plugin permits custom configuration of AI models to be used in code assistance. 

### Local Assistant configuration

To use the CIRRUS Cloud installation of Ollama, configure what is called a "Local Assistant".

Create the Local Assistant configuration file:
```
vi ~/.continue/assistants/LocalAssistantCirrus.yaml
```

Add the following yaml (note the empty models array will be replaced with configured models):
```
name: LocalAssistantCirrus
version: 0.0.1
schema: v1
models: []
```

Replace the empty models array with AI models in the following form (note the usage of openai as the provider and the /v1 path, which establishes the use of the OpenAI API and the use the api key):
```
  - name: ai-model-name:ai-model-version
    title: ai-model-name:ai-model-version
    provider: openai
    model: ai-model-name:ai-model-version
    apiBase: https://sage.code.assist.k8s.ucar.edu/v1
    apiKey: phony-key1
    roles: []
```

Replace the empty roles array with the roles you'd like the model to perform. The possibilities are (edit, apply, embed, chat, autocomplete, rerank). Example:
```
      - edit
      - apply
```

### Example Local Assistant configuration file

See the file [LocalAssistantCirrus.yaml](LocalAssistantCirrus.yaml) as a working example.

## Nvidia GPUs

Per Kevin Hrpcek, we have two types of Nvidia GPU:

[A2](https://www.nvidia.com/en-us/data-center/products/a2/) 16GB

[A10](https://www.nvidia.com/en-us/data-center/products/a10-gpu/) 24GB
