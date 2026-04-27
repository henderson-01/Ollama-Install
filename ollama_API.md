# Ollama API Guide

This guide provides a quick overview of how to interact with the Ollama API to run large language models locally.

## Prerequisites

- [Ollama](https://ollama.com/) must be installed and running on your machine.
- By default, the API is available at: `http://localhost:11434`

## API Endpoints

### 1. Generate a Completion

Use this endpoint to generate a response for a given prompt.

- **Endpoint:** `POST /api/generate`
- **Body Parameters:**
  - `model`: (required) The model name (e.g., `llama3`, `mistral`).
  - `prompt`: (required) The text to send to the model.
  - `stream`: (optional) If `false`, the response will be returned as a single JSON object rather than a stream of objects.

**Example Request (curl):**

```bash
curl http://localhost:11434/api/generate -H "Content-Type: application/json" -d '{
  "model": "llama3",
  "prompt": "Why is the sky blue?",
  "stream": false
}'
```

---

### 2. Chat with a Model

Use this endpoint for multi-turn conversations using a messages list.

- **Endpoint:** `POST /api/chat`
- **Body Parameters:**
  - `model`: (required) The model name.
  - `messages`: (required) A list of message objects with `role` (`system`, `user`, or `assistant`) and `content`.
  - `stream`: (optional) Set to `false` for a single response.

**Example Request (curl):**

```bash
curl http://localhost:11434/api/chat -H "Content-Type: application/json" -d '{
  "model": "llama3",
  "messages": [
    { "role": "user", "content": "Hello!" }
  ],
  "stream": false
}'
```

---

### 3. List Local Models

Retrieve a list of models currently installed on your local machine.

- **Endpoint:** `GET /api/tags`

**Example Request:**

```bash
curl http://localhost:11434/api/tags
```

---

### 4. Pull a Model

Download a model from the Ollama library.

- **Endpoint:** `POST /api/pull`
- **Body:** `{ "name": "llama3" }`

---

## Handling Responses

### Streaming (Default)

Ollama streams responses by default. Each line is a JSON object containing a fragment of the response:

```json
{"model":"llama3","created_at":"...","response":"The","done":false}
```

### Non-Streaming

If you set `"stream": false`, you receive a single final JSON object. Note that the output structure differs slightly depending on the endpoint used:

**For `/api/generate`:**
Returns a `response` string.

```json
{
  "model": "llama3",
  "response": "The sky appears blue because...",
  "done": true,
  "context": [1, 2, 3],
  "total_duration": 12345678
}
```

**For `/api/chat`:**
Returns a `message` object containing the role and content.

```json
{
  "model": "llama3",
  "message": {
    "role": "assistant",
    "content": "Hello! How can I help you today?"
  },
  "done": true,
  "total_duration": 12345678
}
```

## Common Ports & Troubleshooting

- **Default Port:** `11434`
- **Environment Variables:** If you need to access the API from a different device on your network, set `OLLAMA_HOST=0.0.0.0` before starting the Ollama server.
