# Building a Chatbot: OpenAI vs Anthropic

A practical comparison of building the same chatbot with both APIs.

## What We're Building

A simple but realistic chatbot that:
- Maintains conversation history
- Responds naturally to questions
- Handles multi-turn conversations
- Can be extended with custom behavior

**Goal:** See which API is easier to work with, performs better, and costs less.

## Implementation: OpenAI

### Basic Chatbot Structure

```python
from openai import OpenAI

class OpenAIChatbot:
    def __init__(self, api_key, system_prompt="You are a helpful assistant."):
        self.client = OpenAI(api_key=api_key)
        self.system_prompt = system_prompt
        self.conversation_history = []
    
    def chat(self, user_message):
        # Add user message to history
        self.conversation_history.append({
            "role": "user",
            "content": user_message
        })
        
        # Create messages array with system prompt
        messages = [{"role": "system", "content": self.system_prompt}]
        messages.extend(self.conversation_history)
        
        # Get response
        response = self.client.chat.completions.create(
            model="gpt-4-turbo-preview",
            messages=messages,
            temperature=0.7
        )
        
        # Extract assistant's reply
        assistant_message = response.choices[0].message.content
        
        # Add to history
        self.conversation_history.append({
            "role": "assistant",
            "content": assistant_message
        })
        
        return assistant_message
    
    def reset(self):
        """Clear conversation history"""
        self.conversation_history = []

# Usage
chatbot = OpenAIChatbot(api_key="your-key-here")

print(chatbot.chat("Hi! What's the weather like?"))
print(chatbot.chat("What about tomorrow?"))  # Maintains context
```

### What I Learned

✅ **Pros:**
- System prompts are straightforward
- Response structure is simple: just `.content`
- No required parameters beyond messages
- Works exactly as documented

⚠️ **Gotchas:**
- System prompt goes in messages array (not a separate param)
- Need to manually track conversation history
- Can forget to append assistant responses to history
- No built-in conversation management

## Implementation: Anthropic

### Basic Chatbot Structure

```python
import anthropic

class AnthropicChatbot:
    def __init__(self, api_key, system_prompt="You are a helpful assistant."):
        self.client = anthropic.Anthropic(api_key=api_key)
        self.system_prompt = system_prompt
        self.conversation_history = []
    
    def chat(self, user_message):
        # Add user message to history
        self.conversation_history.append({
            "role": "user",
            "content": user_message
        })
        
        # Get response (system prompt is separate!)
        message = self.client.messages.create(
            model="claude-3-5-sonnet-20241022",
            max_tokens=1024,
            system=self.system_prompt,  # ← Different from OpenAI!
            messages=self.conversation_history
        )
        
        # Extract assistant's reply
        assistant_message = message.content[0].text
        
        # Add to history
        self.conversation_history.append({
            "role": "assistant",
            "content": assistant_message
        })
        
        return assistant_message
    
    def reset(self):
        """Clear conversation history"""
        self.conversation_history = []

# Usage
chatbot = AnthropicChatbot(api_key="your-key-here")

print(chatbot.chat("Hi! What's the weather like?"))
print(chatbot.chat("What about tomorrow?"))  # Maintains context
```

### What I Learned

✅ **Pros:**
- System prompt is a **separate parameter** (cleaner!)
- Response quality feels more natural
- Better at following complex instructions
- Handles longer context better

⚠️ **Gotchas:**
- Must specify `max_tokens` every time
- Response is `message.content[0].text` not just `.content`
- Model names include dates (more to type)
- Still need manual history management

## Side-by-Side Comparison

| Feature | OpenAI | Anthropic | Winner |
|---------|--------|-----------|--------|
| **Setup Complexity** | Simple | Simple | Tie |
| **System Prompt** | In messages array | Separate parameter | Anthropic |
| **Required Params** | Just messages | messages + max_tokens | OpenAI |
| **Response Structure** | `.choices[0].message.content` | `.content[0].text` | OpenAI |
| **Context Handling** | 128k tokens | 200k tokens | Anthropic |
| **Response Quality** | Excellent | Excellent | Tie |
| **Following Instructions** | Very good | Better | Anthropic |
| **Speed** | Fast | Fast | Tie |

## Real Conversation Test

I tested both with the same 3-turn conversation:

**Turn 1:** "I'm planning a trip to Japan. What should I see?"  
**Turn 2:** "I only have 5 days. What would you prioritize?"  
**Turn 3:** "What's the best way to get around?"

### Results:

**Context Retention:** Both remembered previous turns perfectly ✅  
**Response Quality:** Claude gave slightly more structured answers  
**Relevance:** Both stayed on topic throughout  
**Personality:** GPT-4 felt more casual, Claude more thoughtful

## Cost Analysis

For a 10-turn conversation (approx 3000 tokens total):

| API | Input Cost | Output Cost | Total Cost |
|-----|------------|-------------|------------|
| GPT-4 Turbo | $0.030 | $0.060 | **$0.090** |
| Claude 3.5 Sonnet | $0.009 | $0.045 | **$0.054** |

**Claude is 40% cheaper** for this use case.

For a chatbot with 10,000 conversations/month:
- OpenAI: ~$900/month
- Anthropic: ~$540/month

**Savings: $360/month with Claude**

## Handling Edge Cases

### Long Conversations

Both APIs will eventually hit context limits. Here's how to handle it:

```python
def trim_history(self, max_messages=20):
    """Keep only recent messages to avoid context limits"""
    if len(self.conversation_history) > max_messages:
        # Keep system context, trim old messages
        self.conversation_history = self.conversation_history[-max_messages:]
```

**OpenAI:** 128k tokens ≈ 96k words ≈ 6,400 messages  
**Anthropic:** 200k tokens ≈ 150k words ≈ 10,000 messages

For most chatbots, you'll trim history for UX reasons before hitting limits.

### Error Handling

```python
def chat_with_retry(self, user_message, max_retries=3):
    """Chat with automatic retry on errors"""
    for attempt in range(max_retries):
        try:
            return self.chat(user_message)
        except Exception as e:
            if attempt == max_retries - 1:
                raise
            time.sleep(2 ** attempt)  # Exponential backoff
```

Both APIs can have intermittent issues - always implement retries.

## Streaming Responses

For better UX, stream responses as they generate:

### OpenAI Streaming

```python
def chat_stream(self, user_message):
    self.conversation_history.append({
        "role": "user",
        "content": user_message
    })
    
    messages = [{"role": "system", "content": self.system_prompt}]
    messages.extend(self.conversation_history)
    
    stream = self.client.chat.completions.create(
        model="gpt-4-turbo-preview",
        messages=messages,
        stream=True
    )
    
    full_response = ""
    for chunk in stream:
        if chunk.choices[0].delta.content:
            content = chunk.choices[0].delta.content
            print(content, end="", flush=True)
            full_response += content
    
    self.conversation_history.append({
        "role": "assistant",
        "content": full_response
    })
    
    return full_response
```

### Anthropic Streaming

```python
def chat_stream(self, user_message):
    self.conversation_history.append({
        "role": "user",
        "content": user_message
    })
    
    full_response = ""
    
    with self.client.messages.stream(
        model="claude-3-5-sonnet-20241022",
        max_tokens=1024,
        system=self.system_prompt,
        messages=self.conversation_history
    ) as stream:
        for text in stream.text_stream:
            print(text, end="", flush=True)
            full_response += text
    
    self.conversation_history.append({
        "role": "assistant",
        "content": full_response
    })
    
    return full_response
```

**Winner:** Anthropic's streaming API is cleaner with the context manager.

## Production Considerations

### Database Integration

Store conversations in a database:

```python
import json

class PersistentChatbot:
    def save_conversation(self, user_id, conversation_id):
        """Save to database"""
        data = {
            'user_id': user_id,
            'conversation_id': conversation_id,
            'history': self.conversation_history,
            'timestamp': datetime.now()
        }
        # Save to your database
        db.conversations.insert(data)
    
    def load_conversation(self, conversation_id):
        """Load from database"""
        data = db.conversations.find_one({'conversation_id': conversation_id})
        self.conversation_history = data['history']
```

### Rate Limiting

```python
from ratelimit import limits, sleep_and_retry

@sleep_and_retry
@limits(calls=50, period=60)  # 50 calls per minute
def chat(self, user_message):
    # Your chat logic here
    pass
```

### Monitoring

Track key metrics:
- Response time
- Token usage
- Error rates
- User satisfaction

Both APIs provide usage data in responses:

```python
# OpenAI
print(f"Tokens used: {response.usage.total_tokens}")

# Anthropic
print(f"Input tokens: {message.usage.input_tokens}")
print(f"Output tokens: {message.usage.output_tokens}")
```

## My Recommendation

**Choose OpenAI if:**
- You want the simplest possible integration
- You're already using other OpenAI features (DALL-E, Whisper)
- You need the most widely documented API
- Cost isn't your primary concern

**Choose Anthropic if:**
- You want better cost efficiency (40% cheaper)
- You need longer context windows (200k vs 128k)
- You prefer cleaner system prompt handling
- You want better instruction-following

**For most chatbots:** Anthropic wins on cost and context length. The API is just as easy once you get past the `max_tokens` requirement.

## Complete Working Example

Here's a full chatbot with both providers:

```python
# chatbot_comparison.py

import os
from openai import OpenAI
import anthropic

class MultiProviderChatbot:
    """Chatbot that can switch between OpenAI and Anthropic"""
    
    def __init__(self, provider="openai"):
        self.provider = provider
        self.conversation_history = []
        
        if provider == "openai":
            self.client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))
            self.model = "gpt-4-turbo-preview"
        else:
            self.client = anthropic.Anthropic(api_key=os.getenv("ANTHROPIC_API_KEY"))
            self.model = "claude-3-5-sonnet-20241022"
    
    def chat(self, user_message):
        self.conversation_history.append({
            "role": "user",
            "content": user_message
        })
        
        if self.provider == "openai":
            response = self.client.chat.completions.create(
                model=self.model,
                messages=self.conversation_history
            )
            reply = response.choices[0].message.content
        else:
            message = self.client.messages.create(
                model=self.model,
                max_tokens=1024,
                messages=self.conversation_history
            )
            reply = message.content[0].text
        
        self.conversation_history.append({
            "role": "assistant",
            "content": reply
        })
        
        return reply

# Try both
if __name__ == "__main__":
    print("Testing OpenAI chatbot:")
    openai_bot = MultiProviderChatbot("openai")
    print(openai_bot.chat("Tell me a joke"))
    print()
    
    print("Testing Anthropic chatbot:")
    anthropic_bot = MultiProviderChatbot("anthropic")
    print(anthropic_bot.chat("Tell me a joke"))
```

## Next Steps

Want to extend this comparison?
- Add function calling / tool use comparison
- Test with different chatbot personalities
- Benchmark response times
- Compare multilingual support
- Test with very long conversations

## Questions or Issues?

Open a discussion in the repo - I'd love to hear about your chatbot experiences!

---

[← Back to Comparisons](../README.md)
