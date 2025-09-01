---
title: DSPy-adapters
date: 2025-09-01  16:23:31.704621
background: bg-[#2858ee]
tags:
  - dspy
  - ai
  - python
categories:
  - Programming
intro: |
  DSPy Adapters Cheat Sheet
---

## DSPy Adapters Overview {.cols-2}

### What are DSPy Adapters? {.primary}

Adapters act as the crucial link between `dspy.Predict` modules and Language Models (LMs). They are responsible for transforming DSPy signatures and user inputs into LM-compatible multi-turn messages and parsing LM responses back into structured DSPy outputs.

- **Translates** DSPy signatures into system messages (task/request/response).
- **Formats** input data according to the signature's request structure.
- **Parses** LM responses into structured `dspy.Prediction` instances.
- **Manages** conversation history and function calls (`dspy.Tool`, `dspy.Image`).

### Adapter Configuration: Global

Sets the adapter for the entire Python process. `dspy.ChatAdapter` is the default if not specified.

```python
import dspy

# Default behavior (ChatAdapter is implicit)
dspy.configure(lm=dspy.LM("openai/gpt-4o-mini"))

# Explicitly set ChatAdapter
dspy.configure(
    lm=dspy.LM("openai/gpt-4o-mini"),
    adapter=dspy.ChatAdapter(),
)
```

### Adapter Configuration: Context-Specific

Applies an adapter only within a specific scope using `dspy.context()`.

```python
import dspy

dspy.configure(lm=dspy.LM("openai/gpt-4o-mini"))

with dspy.context(adapter=dspy.JSONAdapter()):
    # DSPy Predict calls within this block use JSONAdapter
    predict_json = dspy.Predict("fact -> statement")
    result = predict_json(fact="Earth is round")

# Outside the 'with' block, the default adapter (ChatAdapter) is used
predict_chat = dspy.Predict("question -> answer")
```

### Adapter System Flow {.col-span-2 .secondary}

The journey from a DSPy agent call to a structured LM response.

- **User calls DSPy agent.** (`dspy.Module` or `dspy.Predict`)
- **`dspy.Predict` invoked.** (to get the LM response)
- **`Adapter.format()` is called.** (converts signature, inputs, demos into LM messages)
- **LM receives messages.** (generates a response)
- **`Adapter.parse()` converts LM response.** (into structured DSPy outputs)
- **Caller receives parsed outputs.** (from `dspy.Predict`)

{.marker-timeline}

### Inspecting LM Messages {.col-span-2}

You can explicitly call `Adapter.format()` to see the messages sent to the LM or use `dspy.inspect_history()` to view the full interaction with the LM.

```python
import dspy

signature = dspy.Signature("question -> answer")
inputs = {"question": "What is 2+2?"}
demos = [{"question": "What is 1+1?", "answer": "2"}]

adapter = dspy.ChatAdapter()
formatted_messages = adapter.format(signature, demos, inputs)

# Output structure (truncated):
# {'role': 'system', 'content': 'Your input fields are:...'}
# {'role': 'user', 'content': '[[ ## question ## ]]\nWhat is 1+1?...'}
# ...
```

To see the full prompt and response details, use `dspy.inspect_history()` after a `dspy.Predict` call.

## Adapter Types {.cols-2}

### ChatAdapter (Default) {.primary}

The default adapter, compatible with all LMs. It uses a field-based format with special markers to define request and response structures. For non-primitive types, it includes JSON schema.

| When to Use                                       | When Not to Use                                               |
| :------------------------------------------------ | :------------------------------------------------------------ |
| `✔` Universal compatibility                      | `✖` Latency-sensitive applications (more boilerplate tokens) |
| `✔` Automatic fallback to JSONAdapter on failure |                                                               |

#### ChatAdapter Example Setup {.col-span-2}

Defining a signature with a Pydantic model for structured outputs and configuring `ChatAdapter`.

```python
import dspy
import pydantic

dspy.configure(lm=dspy.LM("openai/gpt-4o-mini"), adapter=dspy.ChatAdapter())

class ScienceNews(pydantic.BaseModel):
    text: str
    scientists_involved: list[str]

class NewsQA(dspy.Signature):
    science_field: str = dspy.InputField()
    news: list[ScienceNews] = dspy.OutputField(desc="news")

predict = dspy.Predict(NewsQA)
# To observe the full prompt, call predict and then dspy.inspect_history()
# predict(science_field="Computer Theory", year=2022, num_of_outputs=1)
# dspy.inspect_history()
```

---

**Prompt Structure:** Uses `[[ ## field_name ## ]]` markers for fields and JSON schema descriptions for complex types like `list[ScienceNews]`.

### JSONAdapter {.primary}

Prompts the LM to return JSON data for all output fields specified in the signature. Ideal for models that natively support structured output via the `response_format` parameter, leading to more reliable parsing and lower latency.

| When to Use                        | When Not to Use                                                               |
| :--------------------------------- | :---------------------------------------------------------------------------- |
| `✔` LM supports `response_format` | `✖` Models without native structured output (e.g., small open-source models) |
| `✔` Low latency (minimal tokens)  |                                                                               |

#### JSONAdapter Example Setup {.col-span-2}

Defining a signature for structured outputs and configuring `JSONAdapter`.

```python
import dspy
import pydantic

dspy.configure(lm=dspy.LM("openai/gpt-4o-mini"), adapter=dspy.JSONAdapter())

class ScienceNews(pydantic.BaseModel):
    text: str
    scientists_involved: list[str]

class NewsQA(dspy.Signature):
    science_field: str = dspy.InputField()
    news: list[ScienceNews] = dspy.OutputField(desc="news")

predict = dspy.Predict(NewsQA)
# To observe the full JSON output prompt, call predict and then dspy.inspect_history()
# predict(science_field="Computer Theory", year=2022, num_of_outputs=1)
# dspy.inspect_history()
```

---

**Prompt Structure:** The LM is instructed to directly output a JSON object containing the specified output fields.
