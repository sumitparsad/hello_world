---
tags: [fastapi, python]
---

# FastAPI

**fastapi** → modern, high performance web framework for building api with python

two major python libraries used to make fastapi

- **starlette** → manages how your api receives request and sends back responses
- **pydantic** → validates the data coming from api → [[Pydantic]]

---

## philosophy (fast to run + fast to code)

- existing frameworks in python were slow (latency issue)
- a lot of boilerplate code in existing ways

### why fast to run

```
web server → SGI (server gateway interface) → api code
```

- **WSGI** → in flask (werkzeug library) → gunicorn web server
- **ASGI** → in fastapi (starlette library) → uvicorn web server

### why fast to code

- automatic input validation (pydantic support by default)
- auto generated interactive docs
- seamless integration with modern ecosystem

---

## path parameters

dynamic segments of url used to identify a specific resource

eg. `/patient/{patient_id}`

### Path()

gives metadata, validation rules, and docs hints for path params

- metadata → `title`, `description`, `example`
- numbers → `gt`, `ge`, `lt`, `le`
- strings → `min_length`, `max_length`, regex

---

## query parameters

optional key-value pairs added at end of url, used to pass extra data to server

used for filtering, sorting, searching, pagination → without changing endpoint path

eg. `/sort?sort_by=bmi&order=desc`

- `?` → marks start of query params
- `key=value` → each param is a pair
- `&` → separates multiple params

### Query()

same as `Path()` but for query params

- set default values
- enforce validation rules
- add metadata like description, title, examples

---

## HTTPException

built-in exception to return custom http error responses when something goes wrong

instead of returning normal json or crashing the server, raise error with

- proper http status code like 404, 400, 403
- custom error message
- extra headers (optional)

```python
raise HTTPException(status_code=404, detail="patient not found")
```
