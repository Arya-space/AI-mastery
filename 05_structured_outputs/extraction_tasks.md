# Extraction Tasks

LLMs excel at pulling structured data from unstructured text.

## Entity Extraction
Extract named entities (people, orgs, dates, amounts):
```python
class Entities(BaseModel):
    people: list[str]
    organizations: list[str]
    dates: list[str]
    amounts: list[str]
```
Prompt: "Extract all entities from the text below. Return empty lists if none found."

## Relationship Extraction
```python
class Relationship(BaseModel):
    subject: str
    predicate: str
    object: str

class RelationshipList(BaseModel):
    relationships: list[Relationship]
```

## Table Extraction from Unstructured Text
Convert: "John earns $90k, Mary earns $110k" →
```json
[{"name": "John", "salary": 90000}, {"name": "Mary", "salary": 110000}]
```
Useful for processing invoices, reports, emails.

## Multi-label Classification
```python
class Classification(BaseModel):
    categories: list[Literal["billing", "technical", "general", "urgent"]]
    primary_category: Literal["billing", "technical", "general", "urgent"]
```
Note: allow multiple labels when a ticket can belong to multiple categories.

## Confidence Scores
```python
class ExtractionResult(BaseModel):
    value: str
    confidence: float = Field(ge=0, le=1)
    reasoning: str
```
Use confidence to decide when to route to human review (e.g., confidence < 0.7 → escalate).

## Tips
- Always specify what to return when nothing is found (empty list, null, "N/A")
- Test with documents where entities are absent — LLMs tend to hallucinate if you don't handle this
- Use Pydantic `Optional` for fields that may not always be present
