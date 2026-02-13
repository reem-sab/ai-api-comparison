# Getting Started with Anthropic Claude API

A practical guide to your first Claude API call in 15 minutes.

## Prerequisites

- Python 3.7+ or Node.js 14+
- An Anthropic account
- API credits (free tier available)

## Step 1: Get Your API Key

1. Go to https://console.anthropic.com/settings/keys
2. Click "Create Key"
3. Name it (e.g., "dev-testing")
4. **Copy it immediately** - you won't see it again
5. Store it in a password manager

⚠️ **Never commit your API key to git!**

## Step 2: Install the SDK

### Python
```bash
pip install anthropic
```

### Node.js
```bash
npm install @anthropic-ai/sdk
```

## Step 3: Your First API Call

### Python

```python
import anthropic

client = anthropic.Anthropic(api_key="your-api-key-here")

message = client.messages.create(
    model="claude-3-5-sonnet-20241022",
    max_tokens=1024,
    messages=[
        {"role": "user", "content": "Explain API calls in one sentence."}
    ]
)

print(message.content[0].text)
```

### Node.js

```javascript
import Anthropic from '@anthropic-ai/sdk';

const client = new Anthropic({
  apiKey: process.env.ANTHROPIC_API_KEY
});

const message = await client.messages.create({
  model: 'claude-3-5-sonnet-20241022',
  max_tokens: 1024,
  messages: [
    { role: 'user', content: 'Explain API calls in one sentence.' }
  ]
});

console.log(message.content[0].text);
```

## What Just Happened?

1. You created a client with your API key
2. You sent a message to Claude 3.5 Sonnet
3. The API returned a response
4. You printed the text content

**Cost:** ~$0.0003 (about 3/100ths of a penny - cheaper than OpenAI!)

## Key Differences from OpenAI

### Required Parameter: `max_tokens`
Unlike OpenAI, Claude **requires** you to specify `max_tokens`. This is the maximum length of the response.
- Start with 1024 for short responses
- Use 4096 for longer content
- Goes up to 8192

### Response Structure
OpenAI: `response.choices[0].message.content`  
Claude: `message.content[0].text`

Claude's response is a list because it can return multiple content blocks (text, tool use, etc.)

### Model Names
- `claude-3-5-sonnet-20241022` - Best balance (recommended)
- `claude-3-5-haiku-20241022` - Fastest, cheapest
- `claude-3-opus-20240229` - Most capable (expensive)

⚡ Pro tip: Sonnet is usually the sweet spot for most use cases.

## Common Issues

### "Invalid API key"
- Check for extra spaces
- Ensure you copied the complete key
- Verify it's active in your console

### "max_tokens is required"
- Unlike OpenAI, this isn't optional
- Add `max_tokens=1024` to your request
- Adjust based on your needs

### "Model not found"
- Check the model name spelling
- Make sure you're using the full model string with date
- Claude uses different naming: `claude-3-5-sonnet-20241022` not just `claude-3.5`

### "Overloaded" errors
- Claude's API can get busy during peak times
- Implement retry logic with exponential backoff
- Usually resolves within seconds

## System Prompts (Important!)

Claude works best with system prompts to set behavior:

```python
message = client.messages.create(
    model="claude-3-5-sonnet-20241022",
    max_tokens=1024,
    system="You are a helpful coding assistant. Be concise and provide working code.",
    messages=[
        {"role": "user", "content": "Write a Python function to reverse a string"}
    ]
)
```

System prompts with Claude are more impactful than with OpenAI - use them!

## Next Steps

- [ ] Try different models (haiku is faster, opus is smarter)
- [ ] Experiment with system prompts
- [ ] Test the longer context window (up to 200k tokens!)
- [ ] Explore tool use (Claude's function calling)
- [ ] Read the [official docs](https://docs.anthropic.com)

## 🔄 Coming from OpenAI?

**Main differences:**
- ✅ `max_tokens` is required (not optional)
- ✅ Model names include dates: `claude-3-5-sonnet-20241022`
- ✅ Response structure: `message.content[0].text`
- ✅ Much cheaper input tokens ($3 vs $10 per 1M)
- ✅ Longer context: 200k tokens vs 128k
- ✅ System prompts are more powerful
- ✅ Better at following complex instructions
- ❌ No built-in DALL-E equivalent (use with other image APIs)

**API structure is otherwise very similar - easy migration!**

## Cost Comparison

For the same prompt:

| Provider | Input Cost | Output Cost | Total |
|----------|-----------|-------------|-------|
| Claude 3.5 Sonnet | $3/1M tokens | $15/1M tokens | Cheaper |
| GPT-4 Turbo | $10/1M tokens | $30/1M tokens | More expensive |

For high-volume applications, Claude can save significant money.

## Questions?

Open an issue or discussion in this repo!

---

[← Back to OpenAI Guide](./openai-quickstart.md) | [Compare Both APIs →](../README.md)
