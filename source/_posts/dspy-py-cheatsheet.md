---
title: DSPy Cheatsheet
date: 2025-09-01  16:23:31.704621
background: bg-[#2858ee]
tags:
  - dspy
  - ai
  - python
categories:
  - Programming
intro: |
  DSPy Cheatsheet
---

# DSPy Cheatsheet

- [DSPy Programs](#dspy-programs)
- [DSPy Modules](#dspy-modules)
- [DSPy Metrics](#dspy-metrics)
- [DSPy Evaluation](#dspy-evaluation)
- [DSPy Optimizers](#dspy-optimizers)
- [DSPy Utilities](#dspy-utilities)
- [DSPy Control Flow](#dspy-control-flow)

---

## DSPy Programs

### Forcing Fresh LM Outputs

```python
import dspy

# Initialize a dspy.Predict module
predict = dspy.Predict("question -> answer")

# Call with unique rollout_id and temperature to bypass cache
result = predict(
    question="1+1",
    config={"rollout_id": 123, "temperature": 1.0}
)
print(result.answer)
```

### dspy.Signature {.primary}

```python
import dspy

class BasicQA(dspy.Signature):
    """Answer questions with short factoid answers."""
    question: str = dspy.InputField()
    answer: str = dspy.OutputField(desc="often between 1 and 5 words")

# Example usage (not run here)
# qa_predictor = dspy.Predict(BasicQA)
```

### dspy.ChainOfThought

```python
import dspy

class BasicQA(dspy.Signature):
    question: str = dspy.InputField()
    answer: str = dspy.OutputField(desc="often between 1 and 5 words")

# Create a ChainOfThought module
generate_answer = dspy.ChainOfThought(BasicQA)

# Call the predictor
question = 'What is the color of the sky?'
pred = generate_answer(question=question)
print(pred.answer)
```

### dspy.ProgramOfThought

```python
import dspy

class BasicQA(dspy.Signature):
    question: str = dspy.InputField()
    answer: str = dspy.OutputField(desc="often between 1 and 5 words")

# Create a ProgramOfThought module
pot = dspy.ProgramOfThought(BasicQA)

question = 'Sarah has 5 apples. She buys 7 more. How many now?'
result = pot(question=question)
print(f"Answer: {result.answer}")
```

### dspy.ReAct

```python
import dspy

class BasicQA(dspy.Signature):
    question: str = dspy.InputField()
    answer: str = dspy.OutputField(desc="often between 1 and 5 words")

# Create a ReAct module
react_module = dspy.ReAct(BasicQA)

question = 'Sarah has 5 apples. She buys 7 more. How many now?'
result = react_module(question=question)
print(f"Answer: {result.answer}")
```

## DSPy Modules

### dspy.Retrieve

```python
import dspy

# Configure a Retriever (e.g., ColBERTv2)
colbertv2_wiki17_abstracts = dspy.ColBERTv2(url='http://20.102.90.50:2017/wiki17_abstracts')
dspy.settings.configure(rm=colbertv2_wiki17_abstracts)

# Define Retrieve Module
retriever = dspy.Retrieve(k=3)

# Call the retriever for a query
query = 'When was the first FIFA World Cup held?'
topK_passages = retriever(query).passages

for idx, passage in enumerate(topK_passages):
    print(f'{idx+1}] {passage[:50]}...') # Truncate for display
```

### dspy.CodeAct

```python
import dspy

def factorial(n):
    """Calculate factorial of n"""
    if n == 1: return 1
    return n * factorial(n-1)

# Create a CodeAct module with tools
act = dspy.CodeAct("n->factorial", tools=[factorial])
result = act(n=5)
print(f"Factorial of 5: {result}") # Returns 120
```

### dspy.Parallel

```python
import dspy

# Create a Parallel module with 2 threads
parallel = dspy.Parallel(num_threads=2)
predict = dspy.Predict("question -> answer")

# Run multiple predictions in parallel
result = parallel(
    [
        (predict, dspy.Example(question="1+1").with_inputs("question")),
        (predict, dspy.Example(question="2+2").with_inputs("question"))
    ]
)
# result will be a list of outputs from each prediction
for r in result:
    print(r.answer)
```

## DSPy Metrics

### Function as Metric {.row-span-2}

```python
def parse_integer_answer(answer, only_first_line=True):
    try:
        if only_first_line: answer = answer.strip().split('\n')[0]
        answer = [token for token in answer.split() if any(c.isdigit() for c in token)][-1]
        answer = answer.split('.')[0]
        answer = ''.join([c for c in answer if c.isdigit()])
        return int(answer)
    except (ValueError, IndexError):
        return 0

# Metric Function returns boolean
def gsm8k_metric(gold, pred, trace=None) -> int:
    return int(parse_integer_answer(str(gold.answer))) == int(parse_integer_answer(str(pred.answer)))
```

### LLM as Judge {.row-span-2}

```python
import dspy

class FactJudge(dspy.Signature):
    """Judge if the answer is factually correct based on the context."""
    context = dspy.InputField(desc="Context for the prediction")
    question = dspy.InputField(desc="Question to be answered")
    answer = dspy.InputField(desc="Answer for the question")
    factually_correct: bool = dspy.OutputField(desc="Is the answer factually correct?")

# Use ChainOfThought for the judge
judge = dspy.ChainOfThought(FactJudge)

def factuality_metric(example, pred):
    # Calls the LLM judge to determine factuality
    factual = judge(context=example.context, question=example.question, answer=pred.answer)
    return factual.factually_correct
```

## DSPy Evaluation

### Evaluate Programs {.primary}

```python
from dspy.evaluate import Evaluate

# Placeholder for devset and metric
# devset = [...]
# your_defined_metric = gsm8k_metric # (example from above)

# Initialize evaluator
evaluate_program = Evaluate(
    devset=devset,
    metric=your_defined_metric,
    num_threads=1, # Set based on your needs
    display_progress=True,
    display_table=5 # Display first 5 rows
)

# Run evaluation on your DSPy program
# evaluate_program(your_dspy_program)
```

## DSPy Optimizers

- [LabeledFewShot](#labeledfewshot)
- [BootstrapFewShot](#bootstrapfewshot)
- [Saving/Loading Compiled Programs](#savingloading-compiled-programs)
- [BootstrapFewShotWithRandomSearch](#bootstrapfewshotwithrandomsearch)
- [Ensemble Programs](#ensemble-programs)
- [BootstrapFinetune](#bootstrapfinetune)
- [MIPROv2 (Auto Mode)](#miprov2-auto-mode)

---

### LabeledFewShot

```python
from dspy.teleprompt import LabeledFewShot

# Placeholder for your_dspy_program and trainset
# your_dspy_program = ...
# trainset = [...]

# Create optimizer
labeled_fewshot_optimizer = LabeledFewShot(k=8)

# Compile your program
compiled_program = labeled_fewshot_optimizer.compile(
    student=your_dspy_program,
    trainset=trainset
)
```

### BootstrapFewShot {.row-span-2}

```python
from dspy.teleprompt import BootstrapFewShot

# Placeholder for your_dspy_program, trainset, your_defined_metric
# your_dspy_program = ...
# trainset = [...]

# Create optimizer
fewshot_optimizer = BootstrapFewShot(
    metric=your_defined_metric,
    max_bootstrapped_demos=4,
    max_labeled_demos=16,
    max_rounds=1,
    max_errors=10
)

# Compile your program
compiled_program = fewshot_optimizer.compile(
    student=your_dspy_program,
    trainset=trainset
)
```

### BootstrapFewShot (Custom LM)

```python
from dspy.teleprompt import BootstrapFewShot
import dspy

# Example to use GPT-4 as the teacher LM
gpt4 = dspy.OpenAI(model='gpt-4')

fewshot_optimizer_gpt4_teacher = BootstrapFewShot(
    metric=your_defined_metric,
    max_bootstrapped_demos=4,
    teacher_settings=dict(lm=gpt4) # Specify teacher LM
)

# compiled_program = fewshot_optimizer_gpt4_teacher.compile(
#     student=your_dspy_program,
#     trainset=trainset
# )
```

### Saving/Loading Compiled Programs

```python
import dspy

# Save a compiled program
save_path = './compiled_program_v1.json'
# your_dspy_program_compiled.save(save_path)

# Load a compiled program (requires the class definition)
class YourProgramClass(dspy.Module):
    # ... (define your program structure here)
    pass

loaded_program = YourProgramClass()
loaded_program.load(path=save_path)
print("Program loaded successfully.")
```

### BootstrapFewShotWithRandomSearch {.col-span-2}

```python
from dspy.teleprompt import BootstrapFewShotWithRandomSearch

# Placeholder for your_dspy_program, trainset, devset, your_defined_metric
# your_dspy_program = ... ; trainset = ... ; devset = ...

fewshot_optimizer_rs = BootstrapFewShotWithRandomSearch(
    metric=your_defined_metric,
    max_bootstrapped_demos=2,
    num_candidate_programs=8,
    num_threads=1 # Adjust based on CPU cores
)

compiled_program_rs = fewshot_optimizer_rs.compile(
    student=your_dspy_program,
    trainset=trainset,
    valset=devset
)
```

### Ensemble Programs {.col-span-2}

```python
from dspy.teleprompt import BootstrapFewShotWithRandomSearch
from dspy.teleprompt.ensemble import Ensemble
import dspy

# Compile base programs first
fewshot_optimizer_rs = BootstrapFewShotWithRandomSearch(metric=your_defined_metric, num_candidate_programs=3)
compiled_programs_rs_result = fewshot_optimizer_rs.compile(
    student=your_dspy_program, trainset=trainset, valset=devset
)

# Extract candidate programs
candidate_programs = [x[-1] for x in compiled_programs_rs_result.candidate_programs]

# Create an Ensemble optimizer with a reduction function (e.g., majority voting)
ensemble_optimizer = Ensemble(reduce_fn=dspy.majority)

# Compile the ensemble from the best candidate programs
ensemble_program = ensemble_optimizer.compile(candidate_programs[:3])
```

### BootstrapFinetune {.row-span-2}

```python
from dspy.teleprompt import BootstrapFinetune
import dspy

# Placeholder for model_to_finetune, your_dspy_program, trainset, your_defined_metric
# Finetune program
config = dict(target=model_to_finetune, epochs=2, bf16=True, bsize=6, accumsteps=2, lr=5e-5)
finetune_optimizer = BootstrapFinetune(metric=your_defined_metric)
finetune_program = finetune_optimizer.compile(your_dspy_program, trainset=trainset, **config)

# Load finetuned model and activate in program for evaluation
ckpt_path = "saved_checkpoint_path_from_finetuning"
LM = dspy.HFModel(checkpoint=ckpt_path, model=model_to_finetune)
for p in finetune_program.predictors():
    p.lm = LM
    p.activated = False # Re-activate for new LM
```

### COPRO

```python
from dspy.teleprompt import COPRO
import dspy

# Placeholder for model_to_generate_prompts, your_dspy_program, trainset
# model_to_generate_prompts = dspy.OpenAI(model='gpt-4')

eval_kwargs = dict(num_threads=1, display_progress=True, display_table=0)

copro_teleprompter = COPRO(
    prompt_model=model_to_generate_prompts,
    metric=your_defined_metric,
    breadth=1, # Number of new prompts generated
    depth=1,   # Times to generate prompts
    init_temperature=0.7,
    verbose=False
)

compiled_program_copro = copro_teleprompter.compile(
    your_dspy_program,
    trainset=trainset,
    eval_kwargs=eval_kwargs
)
```

### MIPROv2 (Auto Mode) {.row-span-2}

```python
from dspy.teleprompt import MIPROv2
import dspy

# Placeholder for program to optimize, trainset, devset, gsm8k_metric, evaluate
# program_instance = your_dspy_program.deepcopy()

# Initialize optimizer with auto-configuration
teleprompter = MIPROv2(
    metric=gsm8k_metric,
    auto="light", # Options: light, medium, heavy
)

print("Optimizing program with MIPRO...")
optimized_program = teleprompter.compile(
    program_instance,
    trainset=trainset,
    max_bootstrapped_demos=3,
    max_labeled_demos=4,
)
optimized_program.save(f"mipro_optimized.json")

print("Evaluate optimized program...")
evaluate(optimized_program, devset=devset[:])
```

### MIPROv2 (0-Shot instructions only)

```python
from dspy.teleprompt import MIPROv2
import dspy

teleprompter = MIPROv2(metric=gsm8k_metric, auto="light")

# Optimize instructions only (0-shot)
optimized_program_0shot = teleprompter.compile(
    your_dspy_program.deepcopy(), # Use deepcopy to avoid modifying original
    trainset=trainset,
    max_bootstrapped_demos=0, # No few-shot examples
    max_labeled_demos=0,      # No labeled demos
)

optimized_program_0shot.save(f"mipro_optimized_0shot.json")
evaluate(optimized_program_0shot, devset=devset[:])
```

### KNNFewShot

```python
from sentence_transformers import SentenceTransformer
from dspy import Embedder, ChainOfThought
from dspy.teleprompt import KNNFewShot

# Initialize sentence transformer for embedding
sentence_model = SentenceTransformer("all-MiniLM-L6-v2")
vectorizer = Embedder(sentence_model.encode)

# Create KNNFewShot optimizer
knn_optimizer = KNNFewShot(k=3, trainset=trainset, vectorizer=vectorizer)

# Compile a ChainOfThought module using KNNFewShot
qa_compiled = knn_optimizer.compile(student=ChainOfThought("question -> answer"))
```

### BootstrapFewShotWithOptuna

```python
from dspy.teleprompt import BootstrapFewShotWithOptuna

# Placeholder for your_dspy_program, trainset, devset, your_defined_metric
# your_dspy_program = ... ; trainset = ... ; devset = ...

fewshot_optuna_optimizer = BootstrapFewShotWithOptuna(
    metric=your_defined_metric,
    max_bootstrapped_demos=2,
    num_candidate_programs=8,
    num_threads=1
)

compiled_program_optuna = fewshot_optuna_optimizer.compile(
    student=your_dspy_program,
    trainset=trainset,
    valset=devset
)
```

### SIMBA

```python
from dspy.teleprompt import SIMBA

# Placeholder for your_dspy_program, trainset, your_defined_metric
# your_dspy_program = ... ; trainset = ...

simba = SIMBA(
    metric=your_defined_metric,
    max_steps=12,  # Max optimization steps
    max_demos=10   # Max few-shot demos
)

optimized_program = simba.compile(
    student=your_dspy_program,
    trainset=trainset
)
```

## DSPy Utilities

### dspy.Tool

```python
import dspy

def search_web(query: str) -> str:
    """Search the web for information"""
    # In a real scenario, this would call an API
    return f"Simulated search results for: '{query}'"

# Create a DSPy Tool from a Python function
web_tool = dspy.Tool(search_web)

# Use the tool
result = web_tool(query="Python programming")
print(result)
```

### dspy.streamify {.col-span-2 .row-span-2}

```python
import dspy
import asyncio

predict = dspy.Predict("question->answer")

# Wrap a predictor for streaming output
stream_predict = dspy.streamify(
    predict,
    stream_listeners=[dspy.streaming.StreamListener(signature_field_name="answer")],
)

async def read_output_stream():
    # Call the streaming predictor
    output_stream = stream_predict(question="Why did a chicken cross the road?")

    # Iterate over chunks of the streamed output
    async for chunk in output_stream:
        print(chunk, end='', flush=True) # Print chunks as they arrive

asyncio.run(read_output_stream())
```

### dspy.asyncify

```python
import dspy
import asyncio

dspy_program = dspy.ChainOfThought("question -> answer")

# Convert a DSPy program to an async version
async_program = dspy.asyncify(dspy_program)

# Run the async program
async def run_async_program():
    result = await async_program(question="What is DSPy?")
    print(result.answer)

asyncio.run(run_async_program())
```

### Track Usage

```python
import dspy

# Enable usage tracking
dspy.settings.configure(track_usage=True)

qa_predictor = dspy.Predict("question -> answer")
result = qa_predictor(question="What is 2+2?")

# Retrieve token usage information
usage = result.get_current_usage()
print(f"Token usage for current call: {usage}")

# Total usage for current LM after multiple calls
# print(f"Total LM usage: {dspy.settings.lm.get_usage()}")
```

### dspy.configure_cache

```python
import dspy

# Configure caching settings
# Disable both disk and memory cache
dspy.configure_cache(
    enable_disk_cache=False,
    enable_memory_cache=False,
)

# Example: Re-enable only memory cache
# dspy.configure_cache(enable_memory_cache=True)
```

## DSPy Control Flow

### BestofN

```python
import dspy

qa = dspy.ChainOfThought("question -> answer")

# Define a simple reward function
def one_word_answer(args, pred):
    return 1.0 if len(pred.answer.split()) == 1 else 0.0

# Run module 3 times, pick best by reward_fn, or first passing threshold
best_of_3 = dspy.BestOfN(
    module=qa,
    N=3,
    reward_fn=one_word_answer,
    threshold=1.0
)

result = best_of_3(question="What is the capital of Belgium?").answer
print(result) # Might try multiple times to get "Brussels"
```

### Refine (Basic) {.row-span-2}

```python
import dspy

qa = dspy.ChainOfThought("question -> answer")

def one_word_answer(args, pred):
    return 1.0 if len(pred.answer.split()) == 1 else 0.0

# Refine a module by running it N times with feedback
refine_module = dspy.Refine(
    module=qa,
    N=3,
    reward_fn=one_word_answer,
    threshold=1.0
)

result = refine_module(question="What is the capital of Belgium?").answer
print(result) # Attempts to iteratively refine until threshold or N attempts
```

### Refine (Error Handling)

```python
import dspy

qa = dspy.ChainOfThought("question -> answer")

def always_fail_reward(args, pred):
    raise ValueError("Simulated failure!")

# Set fail_count to control how many errors are tolerated
refine_with_fail_limit = dspy.Refine(
    module=qa,
    N=3,
    reward_fn=always_fail_reward,
    threshold=1.0,
    fail_count=1 # Module will raise error after 1 failure
)

try:
    refine_with_fail_limit(question="Test question")
except ValueError as e:
    print(f"Caught expected error: {e}")

# Default behavior (fail_count = N) allows N failures:
# refine = dspy.Refine(module=qa, N=3, ...)
# refine(question="What is the capital of Belgium?")
```
