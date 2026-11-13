# Repository Guidelines

## Project Scope

This project generates only Python code for Google Colab. All examples and generated code must run in a Colab notebook. Do not introduce other programming languages, standalone applications, or workflows that require a local development environment.

## Project Structure & Module Organization

This workspace currently contains no application code, tests, assets, or dependency manifests. Keep the initial structure simple and update this guide as the project develops.

- Put each standalone example in a descriptively named directory, such as `examples/basic_graph/`.
- Place shared code in a dedicated module only when multiple examples need it.
- Keep automated tests in `tests/` and small fixtures in `tests/fixtures/`.
- Document example prerequisites, configuration, and expected output in a root `README.md` or an example-specific README.

## Build, Test, and Development Commands

Google Colab is the execution environment. Organize examples into ordered notebook cells for dependency installation, imports, configuration, and execution. Use `%pip install` in setup cells for dependencies that Colab does not provide. Document how to open and run each example in `README.md`.

Specify dependency versions when compatibility requires them. Avoid machine-specific paths and local setup requirements. Do not document commands as supported until they have been verified.

## Coding Style & Naming Conventions

Follow Python's PEP 8 conventions, use four-space indentation, and use `snake_case` for files, functions, variables, and graph nodes. Keep examples focused on one concept and explain non-obvious behavior with short comments. Ensure notebook cells execute in order without relying on hidden runtime state.

No formatter or linter is configured. Add shared configuration when adopting these tools and document their invocation.

## Testing Guidelines

No testing framework or coverage threshold is established. Validate examples in a fresh Colab runtime by running all cells from top to bottom. Add Python assertions or tests for new behavior, covering successful execution and relevant failure cases. Keep network-dependent checks separate from deterministic tests. Report when Colab execution has not been verified.

## Commit & Pull Request Guidelines

No Git history is available to establish commit conventions. Use short, imperative commit subjects, such as `Add basic graph example`. Keep changes focused.

Pull requests should describe the purpose, affected examples, and validation performed. Link related issues when applicable and include sample output for observable behavior changes.

## Security & Configuration

Never commit credentials or API keys, including in saved notebook outputs. Use Colab Secrets through `google.colab.userdata` for credentials and provide placeholder configuration examples. Clear sensitive outputs before committing notebooks.
