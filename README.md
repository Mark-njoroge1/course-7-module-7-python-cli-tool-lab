# Task Manager CLI

A modular, object-oriented command-line tool for managing users and their tasks,
built with Python's `argparse`.

## Files

- `models.py` — Pure data/business logic: `Task` and `User` classes (no CLI code).
- `cli_tool.py` — The CLI itself: argparse setup, command handlers, and an
  interactive session mode. Wrapped in `if __name__ == "__main__":`.

## Setup

No third-party dependencies — just Python 3.7+.

```bash
git clone <your-fork-url>
cd course-7-module-7-python-cli-tool-lab
git checkout -b feature-cli-tool
```

## Usage

### 1. Single-shot commands

Each invocation of `python cli_tool.py <command>` starts with a fresh,
empty in-memory store (this is normal — a new process has no memory of
the last one):

```bash
python cli_tool.py add-task Alice "Write unit tests"
python cli_tool.py add-user Bob
python cli_tool.py add-task Alice "Refactor CLI" --priority high
python cli_tool.py list-tasks Alice
python cli_tool.py complete-task Alice "Write unit tests"
python cli_tool.py remove-task Alice "Refactor CLI"
python cli_tool.py list-users
```

### 2. Interactive session (recommended)

Run with no arguments to start a session where data persists across
commands until you type `exit` or `quit`:

```bash
python cli_tool.py
```

```
taskmanager> add-user Alice
👤 User 'Alice' created.
taskmanager> add-task Alice "Write unit tests"
📌 Task 'Write unit tests' added to Alice.
taskmanager> complete-task Alice "Write unit tests"
✅ Task 'Write unit tests' completed.
taskmanager> list-tasks Alice
📋 Tasks for Alice:
  1. [✔] Write unit tests (priority: medium)
taskmanager> exit
👋 Goodbye!
```

Quoted, multi-word titles are supported in both modes (use `"..."` around
the title).

## Commands

| Command | Arguments | Description |
|---|---|---|
| `add-user` | `name` | Create a new user |
| `add-task` | `user title [-p/--priority {low,medium,high}]` | Add a task (creates the user if needed) |
| `complete-task` | `user title` | Mark a task as completed |
| `remove-task` | `user title` | Delete a task |
| `list-tasks` | `user [--completed \| --pending]` | List a user's tasks, optionally filtered |
| `list-users` | — | List all users and their task counts |

Run `python cli_tool.py <command> -h` for details on any command.

## Design notes

- **Separation of concerns**: `models.py` has zero knowledge of argparse or
  the terminal; `cli_tool.py` has zero business logic of its own — it only
  wires user input to `Task`/`User` methods.
- **Validation**: `Task` and `User` raise `TaskError` / `UserError` on
  invalid input (e.g. empty titles/names, bad priority), which `cli_tool.py`
  catches and reports as friendly `❌` messages instead of stack traces.
- **No duplicate tasks**: a user can't have two tasks with the same title
  (case-insensitive).
- **In-memory only**: data is not persisted to disk. Extending this to save
  to a JSON file or database would only require changes in `cli_tool.py`
  (e.g., load `users` from a file at startup, save on exit).