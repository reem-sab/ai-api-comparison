# Getting Started with OpenAI API

A practical guide to your first OpenAI API call in 15 minutes.

## Prerequisites

- Python 3.7+ or Node.js 14+
- An OpenAI account
- $5 minimum credit in your account

## Step 1: Get Your API Key

1. Go to https://platform.openai.com/api-keys
2. Click "Create new secret key"
3. Name it (e.g., "dev-testing")
4. **Copy it immediately** - you won't see it again
5. Store it in a password manager

⚠️ **Never commit your API key to git!**

## Step 2: Install the SDK

### Python
```bash
pip install openai
```

### Node.js
```bash
npm install openai
```

## Step 3: Your First API Call

### Python
```python
from openai import OpenAI

client = OpenAI(api_key="your-api-key-here")

response = client.chat.completions.create(
    model="gpt-4-turbo-preview",
    messages=[
        {"role": "user", "content": "Explain API calls in one sentence."}
    ]
)

print(response.choices[0].message.content)
```

### Node.js
```javascript
import OpenAI from 'openai';

const client = new OpenAI({
  apiKey: process.env.OPENAI_API_KEY
});

const response = await client.chat.completions.create({
  model: 'gpt-4-turbo-preview',
  messages: [
    { role: 'user', content: 'Explain API calls in one sentence.' }
  ]
});

console.log(response.choices[0].message.content);
```

## What Just Happened?

1. You created a client with your API key
2. You sent a message to GPT-4 Turbo
3. The API returned a response
4. You printed it

**Cost:** ~$0.001 (yes, a tenth of a penny)

## Common Issues

### "Incorrect API key"
- Check for extra spaces
- Make sure you copied the whole key
- Verify the key is active in your dashboard

### "Rate limit exceeded"
- You're on the free tier with strict limits
- Add credits to your account
- Wait a few seconds between calls

### "Model not found"
- Check you spelled the model name correctly
- Ensure you have access to GPT-4 (requires $5+ in credits)

## Next Steps

- [ ] Try different models (gpt-3.5-turbo is faster/cheaper)
- [ ] Add system messages to guide behavior
- [ ] Experiment with temperature (0 = focused, 1 = creative)
- [ ] Read the [official docs](https://platform.openai.com/docs)

## Questions?

Open an issue or discussion in this repo!

---

[Compare with Anthropic →](./anthropic-quickstart.md)
```
