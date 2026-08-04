# Restaurant Review Auto-Reply Bot — LLM-Based Conditional Workflow

A LangGraph project that classifies a restaurant review's sentiment, then routes it down one of two paths: a thank-you reply for positive reviews, or a diagnosis + empathetic resolution for negative ones. Built with **Groq** (free) instead of OpenAI.

## How it works

1. `find_sentiment` classifies the review
2. A router decides the path:
```python
def check_sentiment(state) -> Literal["positive_response", "run_diagnosis"]:
    return "positive_response" if state["sentiment"] == "positive" else "run_diagnosis"
```
3. Only **one** branch runs — never both:
```python
graph.add_conditional_edges("find_sentiment", check_sentiment)
```

## Structured output parser

A Pydantic schema forces the LLM to return typed, validated fields instead of free text:
```python
class SentimentSchema(BaseModel):
    sentiment: Literal["positive", "negative"]
```
```python
structured_model = model.with_structured_output(SentimentSchema)
```

## Setup
```bash
pip install langgraph langchain-groq pydantic
```
```python
os.environ["GROQ_API_KEY"] = userdata.get("GROQ_API_KEY")
```

## Run
```python
workflow.invoke({"review": "The food was cold and the waiter was rude."})
```
