---
title: DSPy2
date: 2025-09-01  16:23:31.704621
background: bg-[#2858ee]
tags:
  - dspy
  - ai
  - python
categories:
  - Programming
intro: |
  DSPy2 Signatures.
---

## Table of Contents

- [What are DSPy Signatures?](#what-are-dspy-signatures)
- [Why Use DSPy Signatures?](#why-use-dspy-signatures)
- [Inline DSPy Signatures](#inline-dspy-signatures)
- [Class-Based DSPy Signatures](#class-based-dspy-signatures)
- [Type Resolution in Signatures](#type-resolution-in-signatures)
- [Composing Signatures](#composing-signatures)

---

## What are DSPy Signatures? {.cols-2}

### Signature Definition

A declarative specification of input/output behavior of a DSPy module. They tell the LM _what_ to do, not _how_ to do it.

```text
Function Signatures: describe input/output types.
DSPy Signatures:     declare + initialize behavior.
```

---

### Key Concepts

Field names in DSPy signatures are semantically meaningful.
They define the roles of inputs and outputs.

```python
# Example semantic roles
"question"     != "answer"
"sql_query"    != "python_code"
"document"     != "summary"
```

---

## Why Use DSPy Signatures? {.cols-1}

### Benefits of DSPy Signatures {.primary}

```text
- Modular and clean code
- LM calls optimized into high-quality prompts or finetunes
- Adaptive, reproducible behavior
- DSPy compiler handles prompt/finetune optimization
- Often leads to better prompts than human-written ones
```

DSPy optimizers can try more configurations and tune metrics directly for better results.

---

## Inline DSPy Signatures {.cols-2}

### Basic Inline Syntax

Signatures can be short strings with argument names and optional types.

```python
# Question Answering
signature_qa = "question -> answer"

# Sentiment Classification (default type is str)
signature_sentiment = "sentence -> sentiment: bool"

# Summarization
signature_summary = "document -> summary"
```

---

### Multiple Fields & Types

Signatures can include multiple input/output fields with specific types.

```python
# Retrieval-Augmented QA
rag_signature = "context: list[str], question: str -> answer: str"

# Multiple-Choice QA with Reasoning
mcqa_signature = "question, choices: list[str] -> reasoning: str, selection: int"
```

---

### Adding Instructions

Use the `instructions` keyword argument for runtime variable instructions.

```python
import dspy

# Define a toxicity classifier with instructions
toxicity = dspy.Predict(
    dspy.Signature(
        "comment -> toxic: bool",
        instructions=(
            "Mark as 'toxic' if the comment includes insults, "
            "harassment, or sarcastic derogatory remarks."
        ),
    )
)

# Example usage
comment = "you are beautiful."
result = toxicity(comment=comment).toxic
print(f"Comment: '{comment}'\nToxic: {result}")
# Expected: Toxic: False
```

---

### Sentiment Classification {.col-span-2}

An example using the inline signature for sentiment classification.

```python
import dspy

# Assume dspy.LM is configured
# dspy.configure(lm=dspy.OpenAI(model='gpt-3.5-turbo'))

sentence = "it's a charming and often affecting journey."

# Define and use the classifier
classify = dspy.Predict('sentence -> sentiment: bool')
sentiment_result = classify(sentence=sentence).sentiment

print(f"Sentence: '{sentence}'")
print(f"Sentiment: {sentiment_result}")
# Expected: Sentiment: True
```

---

### Summarization Example {.col-span-2}

Using `dspy.ChainOfThought` which expands the signature with a `reasoning` field.

```python
import dspy

# Assume dspy.LM is configured
# dspy.configure(lm=dspy.OpenAI(model='gpt-3.5-turbo'))

document = """The 21-year-old made seven appearances for the Hammers and netted his
only goal for them in a Europa League qualification round match against Andorran
side FC Lustrains last season. Lee had two loan spells in League One last term,
with Blackpool and then Colchester United. He scored twice for the U's but was
unable to save them from relegation. The length of Lee's contract with the
promoted Tykes has not been revealed. Find all the latest football transfers on
our dedicated page."""

summarize = dspy.ChainOfThought('document -> summary')
response = summarize(document=document)

print("--- Summary ---")
print(response.summary)
# Possible Output: The 21-year-old Lee made seven appearances...
```

---

### ChainOfThought Reasoning {.col-span-2}

`dspy.ChainOfThought` automatically adds a `reasoning` field for the LM's thought process.

```python
import dspy

# Assume 'response' is from the previous Summarization Example
# print(response.summary)

# Access the reasoning field
# Needs a model configured to support ChainOfThought (e.g., GPT-3.5/4)
print("\n--- Reasoning ---")
# Example reasoning from the previous summarization output
print("Reasoning:", response.reasoning)
# Possible Output: Reasoning: We need to highlight Lee's performance for West Ham...
```

---

## Class-Based DSPy Signatures {.cols-1}

### Class-Based Signature Basics

For advanced tasks needing more verbosity.
Inherit from `dspy.Signature` and use a docstring for task clarification.

```python
import dspy
from typing import Literal

class CustomSignature(dspy.Signature):
    """Clarify the overall task here."""
    input_field: str
    output_field: bool
```

---

### InputField & OutputField

Use `dspy.InputField` and `dspy.OutputField` to provide hints (`desc`) and constraints.

```python
import dspy
from typing import Literal

class AdvancedSignature(dspy.Signature):
    """An example signature with descriptions."""

    question: str = dspy.InputField(desc="The user's query.")
    result: Literal['A', 'B'] = dspy.OutputField(
        desc="Choose A or B based on the question."
    )
```

---

### Emotion Classification {.col-span-2}

Example using `Literal` for constrained output types.

```python
import dspy
from typing import Literal

# Assume dspy.LM is configured
# dspy.configure(lm=dspy.OpenAI(model='gpt-3.5-turbo'))

class Emotion(dspy.Signature):
    """Classify emotion."""
    sentence: str = dspy.InputField()
    sentiment: Literal['sadness', 'joy', 'love', 'anger', 'fear', 'surprise'] = dspy.OutputField()

sentence = "i started feeling a little vulnerable when the giant spotlight started blinding me"

# Define and use the classifier
classify = dspy.Predict(Emotion)
prediction = classify(sentence=sentence)

print(f"Sentence: '{sentence}'")
print(f"Predicted emotion: {prediction.sentiment}")
# Possible Output: Predicted emotion: fear
```

---

### Citation Faithfulness {.col-span-2}

A more complex example evaluating if text is faithful to provided context, extracting evidence.

```python
import dspy

# Assume dspy.LM is configured
# dspy.configure(lm=dspy.OpenAI(model='gpt-3.5-turbo'))

class CheckCitationFaithfulness(dspy.Signature):
    """Verify that the text is based on the provided context."""
    context: str = dspy.InputField(desc="facts here are assumed to be true")
    text: str = dspy.InputField()
    faithfulness: bool = dspy.OutputField()
    evidence: dict[str, list[str]] = dspy.OutputField(desc="Supporting evidence for claims")

context = "The 21-year-old made seven appearances for the Hammers and netted his only goal for them in a Europa League qualification round match against Andorran side FC Lustrains last season. Lee had two loan spells in League One last term, with Blackpool and then Colchester United. He scored twice for the U's but was unable to save them from relegation."
text = "Lee scored 3 goals for Colchester United."

faithfulness_checker = dspy.ChainOfThought(CheckCitationFaithfulness)
result = faithfulness_checker(context=context, text=text)

print(f"Text: '{text}'")
print(f"Faithful: {result.faithfulness}")
print(f"Reasoning: {result.reasoning}")
print(f"Evidence: {result.evidence}")
# Possible Output: Faithful: False, Reasoning: '...text states Lee scored 3 goals... context states 'He scored twice'....'
```

---

### Multi-modal Image Classification {.col-span-2}

Using `dspy.Image` for multi-modal tasks.

```python
import dspy

# Assume dspy.LM is configured for multi-modal tasks (e.g., GPT-4o, LLaVA)
# dspy.configure(lm=dspy.OpenAI(model='gpt-4o'))

class DogPictureSignature(dspy.Signature):
    """Output the dog breed of the dog in the image."""
    image_1: dspy.Image = dspy.InputField(desc="An image of a dog")
    answer: str = dspy.OutputField(desc="The dog breed of the dog in the image")

image_url = "https://picsum.photos/id/237/200/300" # A random image URL, likely a dog pic
                                                 # or will generate a generic answer.
                                                 # Actual dog image should be used for best results.
classify_dog = dspy.Predict(DogPictureSignature)
prediction_dog = classify_dog(image_1=dspy.Image.from_url(image_url))

print(f"Image URL: {image_url}")
print(f"Predicted breed: {prediction_dog.answer}")
# Possible Output: Predicted breed: Labrador Retriever (depends on image content)
```

---

## Type Resolution in Signatures {.cols-2}

### Supported Types {.marker-round}

DSPy signatures support a wide range of types for robust and clear definitions.

- **Basic types:** `str`, `int`, `bool`, `float`
- **Typing module:** `list[str]`, `dict[str, int]`, `Optional[float]`, `Union[str, int]`
- **Custom types:** Pydantic `BaseModel` classes
- **Special data types:** `dspy.Image`, `dspy.History`
- **Dot notation:** For nested custom types (e.g., `MyContainer.Query`)

---

### Custom Types in Signatures {.col-span-2}

Define Pydantic models for structured input/output types.

```python
import dspy
import pydantic

# Simple custom type
class QueryResult(pydantic.BaseModel):
    text: str
    score: float

# Signature using a custom type
signature_simple_custom = dspy.Signature("query: str -> result: QueryResult")
print(f"Simple Custom Type Signature: {signature_simple_custom}")

# Nested custom types using dot notation
class MyContainer:
    class Query(pydantic.BaseModel):
        text: str
    class Score(pydantic.BaseModel):
        score: float

signature_nested_custom = dspy.Signature("query: MyContainer.Query -> score: MyContainer.Score")
print(f"Nested Custom Type Signature: {signature_nested_custom}")
```

---

## Composing Signatures {.cols-1}

### Beyond Signatures {.secondary}

Signatures are the building blocks for more complex DSPy pipelines.

- **Compose multiple signatures** into larger [DSPy Modules](_link_to_modules.md).
- **Compile these modules** using [DSPy Optimizers](_link_to_optimizers.md) into optimized prompts or finetunes.

DSPy enables you to define behavior once and let the compiler handle efficient execution.
