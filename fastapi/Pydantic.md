---
tags: [pydantic, python]
---

# Pydantic

library used by [[Intro|fastapi]] to validate data

---

## basics

- type hinting → `name: str`
- validation on type of data → **type validation**
- validation on value of data → **data validation**

---

## steps

1. define pydantic model representing ideal schema of data
	- expected fields, data types, and any validation constraint (eg. `gt=0` for positive numbers)
2. instantiate the model with raw input data (usually dict or json)
	- pydantic will automatically do validation and coerce it into correct python types (if possible)
	- if data doesn't meet model's requirements → raises `ValidationError`
3. pass the validated model object to functions

---

## data types

- `List[str]` → checks list + str values inside both
- `list` → only checks list, not the data inside
- `EmailStr`, `AnyUrl` → pydantic types for data validation

---

## Field()

gives customization to set default values, json schema metadata, constraints

```python
weight: float = Field(gt=0)

name: Annotated[str, Field(
    max_length=50,
    title="Name",
    description="Description",
    examples=["nitish", "sumit"],
)]

married: Annotated[bool, Field(default=None, description="description")]

weight: Annotated[float, Field(gt=0, strict=True)]
```

- `strict=True` → no type coercion, `"70"` won't become `70.0`

---

## field validator

custom validation on a single field, when `Field()` constraints are not enough

- use `@field_validator('field_name')` + `@classmethod`
- return the value (can also transform it, eg. make uppercase)
- `mode='after'` (default) → runs after type coercion
- `mode='before'` → runs on raw input, before coercion

```python
@field_validator('email')
@classmethod
def email_validator(cls, value):
    domain = value.split('@')[-1]
    if domain not in ['hdfc.com', 'icici.com']:
        raise ValueError('not a valid domain')
    return value
```

---

## model validator

validation that needs multiple fields together

- use `@model_validator(mode='after')`
- gets the whole model as `self`, return `self`

```python
@model_validator(mode='after')
def validate_emergency_contact(self):
    if self.age > 60 and 'emergency' not in self.contact_details:
        raise ValueError('patients older than 60 must have emergency contact')
    return self
```

---

## computed fields

field whose value is calculated from other fields, user doesn't send it

- use `@computed_field` + `@property`

```python
@computed_field
@property
def bmi(self) -> float:
    return round(self.weight / (self.height ** 2), 2)
```

---

## nested models

using one model as a field type inside another model

- better organisation, reusability
- validation happens for nested data too
- access like `patient.address.city`

```python
class Address(BaseModel):
    city: str
    state: str
    pin: str

class Patient(BaseModel):
    name: str
    address: Address
```

---

## serialization

converting model object back to dict or json

- dict → `patient.model_dump()`
- json → `patient.model_dump_json()`
- control fields with
	- `include={'name'}` → only these fields
	- `exclude={'age'}` → skip these fields
	- `exclude_unset=True` → only fields user actually sent
