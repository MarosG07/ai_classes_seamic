# Christmas break project — Engineering Knowledge Assistant

**Suggested effort:** 12-16 focused hours across the break

## Goal

Extend the Engineering Knowledge Assistant into a small, testable document service. Users should be able to index a
local collection of PDF, TXT and Markdown documents, inspect the indexed catalogue, and search the extracted text using
simple keyword matching.

This milestone is deliberately **not an AI/RAG implementation**. Do not add an LLM, embeddings or a vector database.
Those topics belong to Class 3.

## How this project continues Class 1

The Class 2 starter includes the same Class 1 project files and tests. It intentionally does not include the Class 1
homework solutions:

- `catalog.py` still contains its four TODOs.
- `tests/test_catalog.py` is retained unchanged.
- The new Class 2 modules also contain TODOs and acceptance tests.

If you completed Class 1, use your own working branch/repository as the source of your Class 1 implementation and carry
that work forward. The public course starter is a clean teaching snapshot, and does not contain previous solutions.

## Functional requirements

### 1. Keep the Class 1 catalogue working

Complete and preserve the four catalogue operations from Class 1:

- detect supported regular files (`.pdf`, `.txt`, `.md`, case-insensitively);
- recursively discover files in deterministic relative-path order;
- describe a document using a relative POSIX path, lower-case extension and file size;
- build the manifest by composing the previous functions.

The supplied Class 1 tests must continue to pass. Do not change their expected behavior.

### 2. Extract document text

Implement `extract_text(path)` in `extractor.py`:

- `.txt` and `.md`: decode UTF-8;
- `.pdf`: use `pypdf` and extract text from all pages in order;
- unsupported extensions: raise `ValueError`;
- do not silently discard decoding or PDF parsing errors.

This is text extraction, not OCR. Scanned PDFs may produce little or no text; document that limitation in your README.

### 3. Split text into chunks

Implement `chunk_text(text, max_chars=1000, overlap=150)` in `chunking.py`.

- Normalize runs of whitespace before splitting.
- Produce deterministic chunks in source order.
- Do not exceed `max_chars`.
- Use the requested character overlap where possible.
- Return an empty list for empty or whitespace-only input.
- Reject invalid window settings with `ValueError`.

Character-based chunking is sufficient for this class. Do not introduce a tokenizer or embedding model.

### 4. Persist metadata and chunks in SQLite

Implement the functions in `database.py`. At minimum, your schema must represent:

**documents**

- integer primary key;
- original filename and relative path;
- extension and file size in bytes;
- SHA-256 content digest;
- creation/indexing timestamp;
- unique constraint on `relative_path`.

**chunks**

- integer primary key;
- foreign key to the document;
- zero-based chunk index;
- chunk text;
- uniqueness of `(document_id, chunk_index)`;
- cascading deletion when a document is deleted.

Use parameterized SQL for all values. Enable SQLite foreign-key enforcement on connections. Re-ingesting a document with
the same relative path must update the stored document/chunks instead of creating duplicates.

### 5. Build the ingestion service

Implement `ingest_directory` in `service.py`.

The service should compose existing functions; it should not reimplement file discovery or metadata logic. For each
supported document:

1. obtain the Class 1 metadata;
2. extract its text;
3. calculate SHA-256;
4. create chunks;
5. persist metadata and chunks.

Return counts for discovered, indexed and skipped files. A failure on one document should not silently corrupt the rest
of the database; document your chosen error-handling policy.

### 6. Expose the service through FastAPI

Implement the routes documented in `api.py`:

| Method | Endpoint                   | Expected behavior                                         |
|--------|----------------------------|-----------------------------------------------------------|
| GET    | `/health`                  | Return `{"status": "ok"}`                                 |
| POST   | `/documents/ingest`        | Ingest the configured documents directory                 |
| GET    | `/documents`               | List indexed document metadata                            |
| GET    | `/documents/{document_id}` | Return one document or 404                                |
| DELETE | `/documents/{document_id}` | Delete a document and its chunks or 404                   |
| GET    | `/search?q=...&limit=10`   | Search chunk text using case-insensitive keyword matching |

Validate request parameters and return appropriate HTTP status codes. Keep route handlers thin: database and ingestion
logic belong in their own modules. The `/search` endpoint is a simple local keyword search, not semantic search.

## Suggested repository workflow

Work in a feature branch in your own copy of the Class 2 starter.

```bash
git checkout -b feat/document-api
uv add fastapi uvicorn pypdf
uv add --group test httpx
make install-test
make lint
make test
```

Implement one layer at a time. After each layer, add or run focused tests. Before opening your pull request:

```bash
make format
make lint
make test
uv run pre-commit install
uv run pre-commit run --all-files
```

Use small Conventional Commits, for example:

```text
feat(extractor): read UTF-8 text documents
feat(chunking): split extracted text into chunks
feat(database): persist document metadata
feat(api): expose document listing endpoint
test(search): cover empty and repeated queries
```

Push your feature branch using the repository's HTTPS remote and open a pull request. Do not commit local documents,
SQLite files, `.venv/`, secrets or IDE caches.

## Acceptance criteria

- [ ] All original Class 1 catalogue tests pass without being modified.
- [ ] Tests cover TXT, Markdown and PDF extraction.
- [ ] Chunking tests cover empty input, invalid settings, overlap and long text.
- [ ] SQLite tests cover insert/update, deterministic listing, search and cascading deletion.
- [ ] API tests cover success, validation errors and not-found responses.
- [ ] At least two additional tests beyond the supplied tests are included.
- [ ] `make lint` passes.
- [ ] `make format` produces no further changes on a second run.
- [ ] `make test` passes.
- [ ] `uv.lock` is committed after adding the Class 2 dependencies.
- [ ] README explains setup, architecture, commands and known limitations.
- [ ] At least four meaningful Conventional Commits are present.
- [ ] A feature branch is pushed and a pull request is opened.

## What is intentionally out of scope

- LLM calls, prompt engineering and generated answers;
- embeddings, vector stores and semantic retrieval;
- OCR for scanned PDFs;
- authentication and multi-user permissions;
- cloud deployment and Docker.

## Reflection questions for the pull request

1. Why should the API layer not contain SQL statements?
2. What does the unique constraint on `relative_path` protect against?
3. Why must SQL values be parameterized?
4. What is the difference between chunking text and embedding text?
5. What happens when the same document is ingested twice?
6. Which failure cases did your additional tests cover?

## Submission

Submit the pull-request URL and a short summary of:

- the architecture you implemented;
- one bug or edge case you discovered;
- one limitation you would address in Class 3.
