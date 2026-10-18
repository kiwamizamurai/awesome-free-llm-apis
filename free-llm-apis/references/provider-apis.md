# Provider APIs - Setup Guides

APIs run by the companies that train or fine-tune the models themselves.

## Google Gemini

**Models:** Gemini 2.5 Pro, Flash, Flash-Lite +4 more
**Limits:** 5-15 RPM, 100-1K RPD
**Region restriction:** Free tier not available in the EEA, UK, or Switzerland.

### Get your API key

1. Go to [Google AI Studio](https://aistudio.google.com/app/apikey).
2. Sign in with a Google account.
3. Click "Create API Key" and select or create a Google Cloud project.
4. Copy the generated key.

### Usage example

```python
from openai import OpenAI

client = OpenAI(
    api_key="YOUR_GEMINI_API_KEY",
    base_url="https://generativelanguage.googleapis.com/v1beta/openai/"
)

response = client.chat.completions.create(
    model="gemini-2.5-flash",
    messages=[{"role": "user", "content": "Hello!"}]
)
print(response.choices[0].message.content)
```

### Environment variable

```bash
export GEMINI_API_KEY="your-key-here"
# Or for OpenAI-compatible usage:
export OPENAI_API_KEY="your-key-here"
export OPENAI_BASE_URL="https://generativelanguage.googleapis.com/v1beta/openai/"
```

---

## Cohere

**Models:** Command A, Command R+, Aya Expanse 32B +9 more
**Limits:** 20 RPM, 1K requests/month

### Get your API key

1. Go to the [Cohere Dashboard](https://dashboard.cohere.com/api-keys).
2. Create an account or sign in.
3. Go to API Keys and generate a trial key.
4. Copy the key.

### Usage example

```python
from openai import OpenAI

client = OpenAI(
    api_key="YOUR_COHERE_API_KEY",
    base_url="https://api.cohere.com/v2/"
)

response = client.chat.completions.create(
    model="command-a-03-2025",
    messages=[{"role": "user", "content": "Hello!"}]
)
print(response.choices[0].message.content)
```

### Environment variable

```bash
export CO_API_KEY="your-key-here"
```

---

## Mistral AI

**Models:** Mistral Large 3, Small 3.1, Ministral 8B +3 more
**Limits:** 1 request/second, 1B tokens/month

### Get your API key

1. Go to the [Mistral Console](https://console.mistral.ai/api-keys).
2. Create an account or sign in.
3. Go to API Keys and create a new key.
4. Copy the key.

### Usage example

```python
from openai import OpenAI

client = OpenAI(
    api_key="YOUR_MISTRAL_API_KEY",
    base_url="https://api.mistral.ai/v1/"
)

response = client.chat.completions.create(
    model="mistral-small-latest",
    messages=[{"role": "user", "content": "Hello!"}]
)
print(response.choices[0].message.content)
```

### Environment variable

```bash
export MISTRAL_API_KEY="your-key-here"
```

---

## Zhipu AI

**Models:** GLM-4.7-Flash, GLM-4.5-Flash, GLM-4.6V-Flash
**Limits:** Undocumented

### Get your API key

1. Go to [Zhipu AI](https://open.bigmodel.cn/usercenter/apikeys).
2. Sign up (a Chinese phone number may be required).
3. Go to API Keys and create a new key.
4. Copy the key.

### Usage example

```python
from openai import OpenAI

client = OpenAI(
    api_key="YOUR_ZHIPU_API_KEY",
    base_url="https://open.bigmodel.cn/api/paas/v4/"
)

response = client.chat.completions.create(
    model="glm-4-flash",
    messages=[{"role": "user", "content": "Hello!"}]
)
print(response.choices[0].message.content)
```

### Environment variable

```bash
export ZHIPU_API_KEY="your-key-here"
```

---

## Sarashina (SB Intuitions)

**Models:** Sarashina3 mini, nano, guard, embedding, rerank, translate
**Limits:** 6-60 RPM per model, counted per model every 60 seconds
**Japanese-focused:** trained on Japanese data, with a Japanese tokenizer. Free beta with no announced end date. Chat Completions is OpenAI-compatible; embeddings, rerank and translate have their own request bodies.

### Get your API key

1. Go to the [Sarashina API key page](https://chat.sarashina.ai/dashboard/api-keys).
2. Sign in with a Google account. No application or credit card needed.
3. Create an API key and copy it.

### Usage example

```python
from openai import OpenAI

client = OpenAI(
    api_key="YOUR_SARASHINA_API_KEY",
    base_url="https://api.sarashina.ai/v1"
)

response = client.chat.completions.create(
    model="sarashina3-mini",
    messages=[{"role": "user", "content": "こんにちは！"}]
)
print(response.choices[0].message.content)
```

### Environment variable

```bash
export SARASHINA_API_KEY="your-key-here"
# Or for OpenAI-compatible usage:
export OPENAI_API_KEY="your-key-here"
export OPENAI_BASE_URL="https://api.sarashina.ai/v1"
```

### Other endpoints

Only Chat Completions is reachable through the OpenAI SDK. `sarashina3-embedding` and `sarashina3-rerank` use their own request bodies, both need `task_type` where noted, and both take a single input per request (problems are batched by sending separate requests). `sarashina3-guard` is called through Chat Completions and returns `安全` or `有害` plus category codes instead of prose.

```bash
# Embeddings: 1,536 dims, task_type required, use cosine similarity
curl https://api.sarashina.ai/v1/embeddings \
  -H "Authorization: Bearer ${SARASHINA_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{"input": "こんにちは", "task_type": "RETRIEVAL_QUERY", "model": "sarashina3-embedding"}'

# Rerank: scores up to 20 documents, returned highest first
curl https://api.sarashina.ai/v1/rerank \
  -H "Authorization: Bearer ${SARASHINA_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{"query": "Sarashinaとは？", "documents": ["更級日記は平安時代の回想録です。", "SarashinaはSB Intuitionsの日本語LLMです。"], "model": "sarashina3-rerank"}'

# Translate: returns {"translated_text": "..."}
curl https://api.sarashina.ai/v1/translate \
  -H "Authorization: Bearer ${SARASHINA_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{"text": "こんにちは", "source_lang": "ja", "target_lang": "en"}'
```

`max_completion_tokens` caps at 8,000 for the chat models, and unsupported OpenAI parameters (like `logprobs`) are rejected with a 400 rather than ignored.
