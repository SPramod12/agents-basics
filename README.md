# Basic Agent: Understanding Agent-Tool Interaction

A minimal, framework-free implementation of an intelligent agent that uses tools to answer user questions. This project demonstrates core agent concepts using a local LLM without relying on frameworks like LangChain, CrewAI, or OpenAI's Agent SDK.

## Overview

This agent implements a **function-calling loop** where:
1. An LLM evaluates the user's question
2. The LLM decides whether to call tools or provide a final answer
3. Tools are executed and results are fed back to the LLM as context
4. The loop continues until a final answer is reached or max steps are exceeded

<img width="1254" height="935" alt="AI Agent" src="https://github.com/user-attachments/assets/556b2d2e-d80c-4c2d-84e6-32d123829324" />

## Key Features

- **Framework-Free**: Pure Python implementation with minimal dependencies
- **Local LLM**: Uses Ollama with Gemma 4 2B model (no API keys needed)
- **Tool-Based Reasoning**: Agent dynamically calls tools to gather information
- **Transparent Loop**: Clear visibility into agent's decision-making process at each iteration

## Prerequisites

### Required
- Python 3.8+
- `requests` library
- `json` library (standard library)

### LLM Setup
1. **Install Ollama** from [ollama.ai](https://ollama.ai)
2. **Pull the Gemma 4 2B model**:
   ```bash
   ollama pull gemma4:e2b
   ```
3. **Start Ollama server**:
   ```bash
   ollama serve
   ```
   This starts a local API at `http://localhost:11434`

## Available Tools

The agent can call two tools to assist with answering questions:

### 1. `get_weather(city: str)`
Returns current weather information for a given city.
```python
get_weather("New York")
# Returns: "The weather in New York is 30°C and sunny."
```

### 2. `celsius_to_custom(celsius: float)`
Converts temperature from Celsius to a custom temperature scale (°T).
```python
celsius_to_custom(30)
# Returns: "40°T"
```

## How It Works

### Agent Loop Flow

```
User Question
    ↓
Send to LLM with System Prompt
    ↓
LLM Response (JSON)
    ↓
Parse Action: "tool_call" or "final"?
    ├─ tool_call → Execute Tool → Add Result to Context → Loop Back
    └─ final → Return Answer
```

### System Prompt

The agent follows a strict system prompt that instructs it to:
- Respond only in JSON format
- Choose between tool calls and final answers based on the question
- Never add extra text or explanations

### LLM Output Format

The LLM outputs JSON in one of two formats:

**For tool calls:**
```json
{
  "action": "tool_call",
  "tools": {
    "tool_name": {
      "arg1": "value",
      "arg2": "value"
    }
  }
}
```

**For final answers:**
```json
{
  "action": "final",
  "tools": [],
  "answer": "your answer"
}
```

## Usage

### Running the Agent

```python
run_agent("What is the temperature in New York? Can you provide that in °T?")
```

### Output Example

```
What is the temperature in New York? Can you provide that in °T?

--- Iteration 1 ---
LLM Output: {"action": "tool_call", "tools": {"get_weather": {"city": "New York"}}}
Tool Used: get_weather
Arguments: {'city': 'New York'}

--- Iteration 2 ---
LLM Output: {"action": "tool_call", "tools": {"celsius_to_custom": {"celsius": 30}}}
Tool Used: celsius_to_custom
Arguments: {'celsius': 30}

--- Iteration 3 ---
LLM Output: {"action": "final", "tools": [], "answer": "The temperature in New York is 30°C, which converts to 40°T."}

The temperature in New York is 30°C, which converts to 40°T.
```

## Key Parameters

- **`max_steps`** (default: 5): Maximum iterations before stopping
  - Prevents infinite loops
  - Allows early termination if answer takes too long

## Learning Takeaways

This implementation teaches:
1. **Agent Loop Pattern**: How function-calling agents reason and act
2. **Tool Use**: How agents decide which tools to invoke
3. **Context Management**: How to maintain conversation state
4. **JSON Parsing**: Structured communication with LLMs
5. **Agentic Reasoning**: When to call tools vs. when to answer directly

## Limitations & Future Improvements

### Current Limitations
- Single-turn conversation (no memory between calls)
- Small model (2B) may have limited reasoning capability
- Tools are hardcoded (not dynamically defined)
- No error handling for malformed LLM responses beyond basic JSON parsing

### Potential Enhancements
- Add conversation history for multi-turn interactions
- Implement more sophisticated tools (calculator, web search, etc.)
- Use larger/more capable models
- Add retry logic for failed tool calls
- Implement tool parameter validation
- Add logging and monitoring

## Dependencies

```
requests
```

Install with:
```bash
pip install requests
```

## License

This is an educational project. Feel free to experiment with additional tools and prompt.

## References

- [Ollama Documentation](https://ollama.ai)
- [Gemma Model](https://ai.google.com/gemma/)
- [Function Calling Pattern](https://platform.openai.com/docs/guides/function-calling)
