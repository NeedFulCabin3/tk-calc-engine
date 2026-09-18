# TK Calc Engine
> An event-driven desktop calculator GUI engine featuring dynamic layout management, real-time expression processing, and active session audit logging.

## Overview
 desktop GUI applications built with default standard library primitives often suffer from state synchronization issues, leaky event handling, and brittle UI scaling. `tk-calc-engine` solves this operational friction by decoupling the expression state handling from layout generation. It delivers a reliable, fully reactive desktop calculator interface that processes mathematical expressions in an isolated scope while maintaining active execution logs.

## How It Works
The engine uses an event-driven loop backed by Tkinter's native `Tk` runtime.
```bash
[ User Input (Button / Keyboard) ]
│
▼
[ Key Binding Handler ]
│
▼
[ Safe Expression Sanitization ] ──> Strips builtins, maps math symbols
│
▼
[ Restricted Eval Execution ] ───> Intercepts ZeroDivisionError / SyntaxError
│
▼
[ State & History Pipeline ] ───> Updates Display Vector & Appends to History
```

* **State Synchronization:** Operates on an internal string buffer (`self.expression`) synchronized directly to the input display widget using dynamic TK text buffer operations (`delete` / `insert`).
* **Evaluation Pipeline:** Mathematical evaluations run through Python's dynamic evaluator isolated inside an empty global namespace dictionary (`{"__builtins__": None}`) to eliminate execution security risks. Expression string transforms handle percentage conversion and exponent replacement prior to computation.
* **Layout Mechanics:** The GUI employs a dual-frame structure within a fixed root container (`360x480`). Toggling history dynamically resizes the native OS container to (`560x480`) and updates layout packing rules on demand without requiring redraw passes.

## Key Features
* **Restricted Math Execution:** Evaluates arithmetic sequences (`+`, `-`, `*`, `/`, `^`, `%`) with error handling for zero division and invalid syntax.
* **Unary Operator Pipelines:** Executes square roots (`√`) and squaring (`x²`) directly against current display state via functional lambdas.
* **Dynamic Frame Resizing:** Expandable side panel showing chronological calculation logs with clickable record loading.
* **Hardware Keyboard Binding:** Intercepts physical keypresses (`Return`, `BackSpace`, `Escape`, numeric keys) using `<Key>` listener events.

## Tech Stack & Core Dependencies
* **Python Version:** Python `3.10+` target.
* **Dependencies:** Zero external vendor packages required. Uses core standard library modules:
  * `tkinter`: Desktop GUI layout construction, widget binding, and event-driven runtime.
  * `math`: Primary calculation routines (`math.sqrt`).

## Environment & Web-Based Quick Start

### Running in GitHub Codespaces
1. Launch a Codespace directly on this repository.
2. Open a terminal and launch virtual frame buffer (Xvfb) if running in headless Linux containers:
```bash
sudo apt-get update && sudo apt-get install -y python3-tk xvfb
xvfb-run python main.py
```

### Local Virtual Environment

#### Using Standard ```venv:```
```bash
python3 -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
python main.py
```

#### Using ```uv:```
```bash
uv run main.py
```

## Repository Structure
```bash
.
├── .github/
│   └── workflows/
│       └── ci.yml             # Code linting and static analysis checks
├── .gitignore                 # Optimized Python artifacts ignore file
├── LICENSE                    # MIT License
├── README.md                  # System architecture documentation
└── main.py                    # Core Application Entrypoint & GUI Engine
```

## Roadmap

**AST Evaluation Migration:** Replace restricted standard evaluation functions with standard ast.literal_eval or custom AST node visitor parsing to enforce strict arithmetic grammars.

**Async Event Dispatcher:** Refactor long-running or floating-point operations using asyncio or explicit thread pools to keep UI paint cycles off the main thread.

**State Persistence:** Implement local file serialization (json or sqlite3) to save calculation audit logs across application restarts.
