# Class 2 – Python Application Development

Welcome to the second session!

In Class 1, we introduced the development environment, Python project tooling, quality checks and Git collaboration. In
this class, we move from standalone Python code to a small application with an HTTP interface, persistent data and
tests.

We continue the **Engineering Knowledge Assistant** project. The goal of this milestone is a document catalogue and
keyword-search API.

## Learning objectives

By the end of this class, you should be able to:

### 1. Understand HTTP and REST

- Explain requests, responses, methods, URLs, headers and status codes.
- Recognize JSON as a common API data format.
- Distinguish resource-oriented REST endpoints from implementation details.
- Use API documentation to inspect and try endpoints.

### 2. Build Python APIs with FastAPI

- Define routes and HTTP methods.
- Validate input and shape output with Pydantic models.
- Return meaningful status codes and errors.
- Run an application with Uvicorn.
- Inspect the generated OpenAPI/Swagger UI documentation.

### 3. Persist data with SQLite and SQL

- Model documents and text chunks as related tables.
- Use primary keys, foreign keys, unique constraints and indexes.
- Write parameterized SQL queries.
- Understand transactions and cascading deletion.

### 4. Design a document-processing pipeline

- Extract text from PDF, TXT and Markdown files.
- Split long text into deterministic overlapping chunks.
- Persist metadata and chunks.
- Implement a simple keyword search over stored chunks.

### 5. Test and debug application behavior

- Test service and database functions independently.
- Exercise API routes with FastAPI's test client.
- Test validation, success and error cases.
- Keep the layers independently understandable and maintainable.

## Practical session

The guided notebook uses a small **notes API** as a teaching example. It demonstrates HTTP requests/responses, Pydantic
validation, SQLite persistence, FastAPI routes and API tests.

The notes example is separate from the Engineering Knowledge Assistant homework. It teaches the techniques without
implementing the project assignment for you.

## Christmas break project

The project continues the Engineering Knowledge Assistant from Class 1. You will add document extraction, chunking,
SQLite persistence, an ingestion service and a REST API with keyword search.

**The public starter contains no homework solutions.** It includes the Class 1 files, with the four Class 1 catalogue
functions still unfinished, and adds new Class 2 TODOs. If you completed Class 1, carry your own work forward from your
personal repository. See the homework brief for details.

## Materials

| Resource                                                              | Description                                                  |
|-----------------------------------------------------------------------|--------------------------------------------------------------|
| [Practical notebook](practical_lab.ipynb)                             | Guided HTTP, FastAPI, Pydantic, SQLite and testing exercises |
| [Christmas project brief](homework.md)                                | Requirements, milestones and acceptance criteria             |
| [Homework starter project](engineering-knowledge-assistant/README.md) | Incremental Python repository with Class 1 and Class 2 TODOs |

## Before starting

Please make sure you have:

- Linux, Ubuntu on WSL2 (Windows), or macOS.
- Python >= 3.12.
- `uv`, Git and GNU Make.
- Your own working copy of the Class 1 project.
- A GitHub account.
- PyCharm or another IDE configured to use the project's `.venv`.

From the Class 2 starter project, add the libraries used in this session:

```bash
cd class_2/engineering-knowledge-assistant
uv add fastapi uvicorn pypdf
uv add --group test httpx
uv add --group dev ipykernel
make install-test
```

The `uv add` commands update `pyproject.toml` and `uv.lock`. Commit both files with your work.

---

**Remember:** the goal is not just to make an endpoint return JSON. It is to design an application whose API, business
logic, persistence and tests have clear responsibilities.
