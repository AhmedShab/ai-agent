# AI Agent

A small local AI coding agent that uses Google's GenAI SDK to generate function-call plans and perform file operations in a constrained working directory. It provides helper functions to list files, read file contents, write files, and execute Python files safely.

This repository is intended as a minimal example and a starting point for building an LLM-powered developer assistant that can inspect and modify a codebase.

## Features

- List files and directories with sizes (constrained to a working directory)
- Read file contents with truncation safeguards
- Write or overwrite files, creating parent directories when needed
- Execute Python files (with basic safety checks)

## Requirements

- Python 3.10+
- The project dependencies are declared in `pyproject.toml` and include:
	- `google-genai` (Gemini / GenAI SDK)
	- `python-dotenv`

Install dependencies:

```bash
pip install google-genai==1.12.1 python-dotenv==1.1.0
```

## Configuration

Create a `.env` file in the project root and add your Gemini / GenAI API key:

```env
GEMINI_API_KEY=your_api_key_here
```

## Usage

The main CLI entrypoint is `main.py`. It accepts a single prompt argument and an optional `--verbose` flag.

```bash
python main.py "Run tests and show me files in the calculator package"
python main.py "List files" --verbose
```

The agent will contact the GenAI API and may plan one or more function calls. The functions exposed to the model are declared as `schema_*` objects in the `functions/` package.

## Functions module overview

Each helper in `functions/` exposes a `types.FunctionDeclaration` schema for the GenAI model and a corresponding implementation:

- `functions/get_files_info.py` — `schema_get_files_info` / `get_files_info(working_directory, directory='.')`
- `functions/get_file_content.py` — `schema_get_file_content` / `get_file_content(working_directory, file_path)`
- `functions/write_file.py` — `schema_write_file` / `write_file(working_directory, file_path, content)`
- `functions/run_python_file.py` — `schema_run_python_file` / `run_python_file(working_directory, file_path, args=[])`

Notes:
- The functions validate paths to ensure they stay within the provided `working_directory` for safety.
- `run_python_file.py` executes processes with a timeout; review and harden if you plan to run untrusted code.


## Testing

There is a `tests.py` file with quick checks. Run it while in the project root and with the virtual environment active.

```bash
python tests.py
```

## License

This project is licensed under the MIT License.
