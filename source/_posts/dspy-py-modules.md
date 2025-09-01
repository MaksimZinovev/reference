---
title: DSPy-modules
date: 2025-09-01  16:23:31.704621
background: bg-[#2858ee]
tags:
  - dspy
  - ai
  - python
categories:
  - Programming
intro: |
  DSPy Modules.
---

## DSPy Modules: Quick Reference {.cols-2}

### Module Core Concepts

- **Building Blocks**: Fundamental components for programs using LMs.
- **Abstract Prompting**: Each module abstracts a prompting technique (e.g., Chain of Thought, ReAct).
- **Generalized**: Designed to handle any `dspy.Signature`.
- **Learnable Parameters**: Contain learnable components (e.g., prompt pieces, LM weights).
- **Composable**: Can be nested to form larger, more complex programs.
- **PyTorch Inspired**: Designed like NN modules, but for LM programs.

---

A DSPy module is a callable object that takes structured inputs, executes an LLM based on its internal logic, and returns structured outputs.

### Basic `dspy.Predict` Usage

The most fundamental module. You declare it with a signature, then call it with input arguments.

```python
import dspy

# Ensure an LM is configured, e.g.:
# dspy.settings.configure(lm=dspy.OpenAI(model='gpt-3.5-turbo'))

sentence = "it's a charming and often affecting journey."

# 1) Declare the module with a signature: 'input_field -> output_field'
classify = dspy.Predict('sentence -> sentiment: bool')

# 2) Call the module with input argument(s).
response = classify(sentence=sentence)

# 3) Access the output field.
print(response.sentiment)
```

```text
True
```

### `dspy.ChainOfThought` with `n` Completions

`dspy.ChainOfThought` injects a `reasoning` step. `n` requests multiple completions for diversity.

```python
import dspy

question = "What's great about the ColBERT retrieval model?"

# Declare with a signature, request 5 completions
cot_predict = dspy.ChainOfThought('question -> answer', n=5)

# Call the module with input
response = cot_predict(question=question)

# Access all 'answer' completions as a list
print(response.completions.answer)
```

```text
['One great thing about the ColBERT retrieval model is...', 'Its ability to efficiently retrieve relevant information...', ...]
```

### Accessing `ChainOfThought` Outputs

`dspy.ChainOfThought` automatically adds a `reasoning` field. Access specific completions or fields.

```python
# Assuming 'response' from previous example (with n=5)

# Reasoning and answer for the 'best' (first) completion
print(f"Reasoning: {response.reasoning}")
print(f"Answer: {response.answer}")

# Access a specific completion (e.g., the third one)
third_completion = response.completions[2]
print(f"Third Reasoning: {third_completion.reasoning}")
print(f"Third Answer: {third_completion.answer}")

# All reasonings or answers as lists
all_reasonings = response.completions.reasoning
all_answers = response.completions.answer
```

## DSPy Module Types

### Built-in DSPy Modules {.col-span-2}

| Module                      | Description                                                                                         |
| :-------------------------- | :-------------------------------------------------------------------------------------------------- |
| `dspy.Predict`              | Basic LM call. Handles learning, stores instructions/demonstrations. Does not modify the signature. |
| `dspy.ChainOfThought`       | Teaches the LM to output step-by-step `reasoning` before the final response. Improves quality.      |
| `dspy.ProgramOfThought`     | Teaches the LM to output code, whose execution results dictate the response.                        |
| `dspy.ReAct`                | An agent module that uses external tools (e.g., search, math) to fulfill its signature.             |
| `dspy.MultiChainComparison` | Compares multiple `ChainOfThought` outputs to produce a final, refined prediction.                  |

{.show-header}

### Function-style Modules

| Module          | Description                                                                          |
| :-------------- | :----------------------------------------------------------------------------------- |
| `dspy.majority` | Performs basic voting to return the most popular response from a set of predictions. |

{.show-header}

## DSPy Module examples {.cols-2}

### Math Problem {.col-span-2}

Using `dspy.ChainOfThought` for a mathematical question.

```python linenums="1"
import dspy
# dspy.settings.configure(lm=dspy.OpenAI(model='gpt-3.5-turbo'))

math_cot = dspy.ChainOfThought("question -> answer: float")
prediction = math_cot(question="Two dice are tossed. What is the probability that the sum equals two?")

print("Reasoning:", prediction.reasoning)
print("Answer:", prediction.answer)
```

```text
Reasoning: When two dice are tossed, each die has 6 faces, resulting in...
Answer: 0.0277776
```

### Retrieval-Augmented Generation (RAG) {.col-span-2}

Integrating a search tool with `dspy.ChainOfThought` for RAG.

```python linenums="1"
import dspy

# Define a simple search function (ColBERTv2 example)
def search(query: str, k: int=3) -> list[str]:
    # Placeholder for actual search implementation (e.g., ColBERTv2)
    # The URL `http://20.102.90.50:2017/wiki17_abstracts` is an example.
    # dspy.ColBERTv2 would dynamically fetch from a configured service.
    # For this cheatsheet, assume it returns relevant text.
    return [f"Abstract for '{query}': Example relevant text...",]

rag_cot = dspy.ChainOfThought('context, question -> response')

question = "What's the name of the castle that David Gregory inherited?"
context_docs = search(question) # Execute search tool

prediction = rag_cot(context=context_docs, question=question)

print(prediction.response)
```

```text
Kinnairdy Castle
```

### Classification with Custom Signature {.col-span-2}

Using `dspy.Predict` with a custom `dspy.Signature` for classification.

```python linenums="1"
import dspy
from typing import Literal

# Define a custom signature for sentiment classification
class Classify(dspy.Signature):
    """Classify sentiment of a given sentence."""
    sentence: str = dspy.InputField()
    sentiment: Literal['positive', 'negative', 'neutral'] = dspy.OutputField()
    confidence: float = dspy.OutputField()

classify_module = dspy.Predict(Classify)
prediction = classify_module(sentence="This book was super fun to read, though not the last chapter.")

print("Sentiment:", prediction.sentiment)
print("Confidence:", prediction.confidence)
```

```text
Sentiment: positive
Confidence: 0.75
```

### Information Extraction {.col-span-2}

Extracting structured information using `dspy.Predict` with a complex output signature.

```python linenums="1"
import dspy

text = "Apple Inc. announced its latest iPhone 14 today. The CEO, Tim Cook, highlighted new features."

# Signature defining multiple output fields
ie_module = dspy.Predict("text -> title, headings: list[str], entities: list[dict[str, str]]")
response = ie_module(text=text)

print("Title:", response.title)
print("Headings:", response.headings)
print("Entities:", response.entities)
```

```text
Title: Apple Unveils iPhone 14
Headings: ['Introduction', 'Key Features', "CEO's Statement"]
Entities: [{'entity': 'Apple Inc.', 'type': 'Organization'}, {'entity': 'iPhone 14', 'type': 'Product'}, {'entity': 'Tim Cook', 'type': 'Person'}]
```

### Agents with `dspy.ReAct` {.col-span-2}

Using `dspy.ReAct` to build an agent that uses tools like evaluation and search.

```python linenums="1"
import dspy

# Define tools for the agent
def evaluate_math(expression: str) -> float:
    # Requires dspy.PythonInterpreter for safe execution
    return dspy.PythonInterpreter().execute(expression)

def search_wikipedia(query: str) -> str:
    # Similar to RAG example, would use ColBERTv2 or other retriever
    return f"Wikipedia abstract for '{query}': David Gregory (1659-1708) was a Scottish mathematician..."

# Declare ReAct module with callable tools
agent = dspy.ReAct("question -> answer: float", tools=[evaluate_math, search_wikipedia])

question = "What is 9362158 divided by the year of birth of David Gregory?"
prediction = agent(question=question)

print(prediction.answer)
```

```text
5761.328
```

## Composing Modules

### Create a Custom Module (`dspy.Module`) {.col-span-2}

Compose multiple DSPy modules into a larger, custom `dspy.Module` class by defining `__init__` and `forward` methods.

```python linenums="1"
import dspy

# Assume 'search' function (e.g., from RAG example) is defined in scope
# def search(query: str, k: int) -> list[str]: ...

class Hop(dspy.Module):
    def __init__(self, num_docs=3, num_hops=2):
        super().__init__() # Initialize base dspy.Module
        self.num_docs, self.num_hops = num_docs, num_hops
        self.generate_query = dspy.ChainOfThought('claim, notes -> query')
        self.append_notes = dspy.ChainOfThought('claim, notes, context -> new_notes: list[str], titles: list[str]')

    def forward(self, claim: str) -> dspy.Prediction:
        notes = []
        titles = []
        for _ in range(self.num_hops):
            query = self.generate_query(claim=claim, notes=notes).query
            context = search(query, k=self.num_docs) # Calls external search
            prediction = self.append_notes(claim=claim, notes=notes, context=context)
            notes.extend(prediction.new_notes)
            titles.extend(prediction.titles)
        return dspy.Prediction(notes=notes, titles=list(set(titles))) # Return Prediction
```

### Invoke a Custom Module

Instantiate your custom `dspy.Module` class and call its `forward` method via `__call__`.

```python
# Assuming 'Hop' class and 'search' function are defined
# dspy.settings.configure(lm=dspy.OpenAI(model='gpt-3.5-turbo'))

hop_module = Hop(num_hops=1) # Use fewer hops for brevity
result = hop_module(claim="Stephen Curry is the best 3 pointer shooter ever.")
print("Notes:", result.notes)
print("Titles:", result.titles)
```

```text
Notes: ['Stephen Curry is known for his exceptional three-point shooting.']
Titles: ['Stephen_Curry']
```

## LM Usage Tracking {.cols-2}

### Enable Usage Tracking

Tracking LM usage is available in DSPy v2.6.16+ and is enabled via `dspy.settings.configure`.

```python
import dspy

# Configure an LM, if not already done:
# dspy.settings.configure(lm=dspy.OpenAI(model='gpt-4o-mini', cache=False))

# Enable usage tracking
dspy.settings.configure(track_usage=True)
print("LM usage tracking enabled.")
```

### Accessing Usage Statistics

After a module call, retrieve usage statistics from the returned `dspy.Prediction` object.

```python
# Assuming track_usage is enabled and LM is configured.
classify = dspy.Predict('sentence -> sentiment: bool')
output = classify(sentence="What a great day!")

# Get usage for the prediction
usage_stats = output.get_lm_usage()
print("LM Usage for prediction:", usage_stats)
```

```text
LM Usage for prediction: {
    'openai/gpt-4o-mini': {
        'completion_tokens': 1,
        'prompt_tokens': 20,
        'total_tokens': 21,
        # ... more details ...
    }
}
```

### Usage with Caching

When DSPy's caching is enabled (`cache=True`), cached responses won't count toward new usage.

```python
import dspy

# Configure with caching enabled
dspy.settings.configure(
    lm=dspy.OpenAI(model='gpt-4o-mini', cache=True), # Note: cache=True
    track_usage=True
)

class MyTrackingProgram(dspy.Module):
    def __init__(self):
        super().__init__()
        self.predictor = dspy.Predict("input -> output")
    def forward(self, input_text: str):
        return self.predictor(input=input_text)

program = MyTrackingProgram()

# First call: will hit the LLM, shows usage statistics
output1 = program(input_text="What is the capital of Zambia?")
print("First call usage:", output1.get_lm_usage())

# Second call (same input): will use cache, shows empty usage dict
output2 = program(input_text="What is the capital of Zambia?")
print("Second call usage:", output2.get_lm_usage())
```

```text
First call usage: {'openai/gpt-4o-mini': {'completion_tokens': 10, 'prompt_tokens': 50, 'total_tokens': 60, ...}}
Second call usage: {}
```
