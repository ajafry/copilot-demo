# GitHub Copilot Features Demo: Streamlit Developer Lab

Use this demo to explore GitHub Copilot while working on the Streamlit application in this repository. The flow follows the feature coverage of the [source demo](https://github.com/ps-copilot-sandbox/copilot-intermediate-gallery-repo/blob/main/demos/features-demo.md), but all prompts, files, and implementation tasks are tailored to this Python project.

## What You Will Learn

By the end of this demo, you will be able to:

- Discover Copilot chat modes, slash commands, tools, and participants.
- Ask Copilot to explain an unfamiliar repository and selected Python code.
- use inline code completion from a docstring and nearby context.
- Plan and implement a repository improvement with Copilot.
- Review AI-generated changes and create a commit message.

Estimated time: 15-20 minutes.

## Before the Demo

1. Open this repository in VS Code.
2. Create a working branch:

   ```powershell
   git switch -c <your-name>-copilot-demo
   ```

3. Create and activate a virtual environment if one is not already active:

   ```powershell
   py -3.13 -m venv .venv
   Set-ExecutionPolicy -Scope Process -ExecutionPolicy RemoteSigned
   .\.venv\Scripts\Activate.ps1
   ```

4. Install the dependencies and start the application:

   ```powershell
   python -m pip install -r requirements.txt
   streamlit run app.py
   ```

5. Open the local URL shown by Streamlit, normally `http://localhost:8501`.

Keep the application running so you can refresh it after code changes.

## Step 1: Discover Copilot Features

Mode: Ask

Session: Local

Goal: Learn what is available before asking Copilot to change code.

The exact menu entries vary by VS Code version, installed extensions, and enabled tools. Use the menus in your environment as the source of truth.

### 1.1 Slash Commands: `/`

Type `/` in Copilot Chat without submitting it. Slash commands are reusable instructions for common development tasks. Look for examples such as:

- `/explain`: explain selected code or a referenced symbol.
- `/fix`: propose a correction for selected code or a reported problem.
- `/tests`: generate tests for selected code.
- `/doc`: add or improve code documentation.
- `/new`: scaffold a new project or file from a description.

Try one now:

1. Open `views/calculator.py` and select the `_backspace` function.
2. Enter `/explain` in Copilot Chat.
3. Confirm that the response describes how the function updates both session-state values.

If a listed command is not available, choose a similar command shown in your `/` menu.

### 1.2 Context References and Tools: `#`

Type `#` without submitting it. These entries attach precise context or expose a tool to the current prompt. Common examples include:

- `#file`: attach a specific file, such as `views/calculator.py`.
- `#selection`: attach the code currently selected in the editor.
- `#codebase`: search or reason across the workspace rather than one file.
- `#problems`: include diagnostics from the Problems panel.
- `#terminalSelection` or `#terminalLastCommand`: include terminal text or the most recent command.
- `#changes`: include current source-control changes when available.

Try a file-grounded prompt. Select `#file`, choose `views/calculator.py`, and submit:

```text
#file Explain the responsibilities of this module and identify where calculator
state is initialized, changed, and displayed. Do not edit the file.
```

Then type `#` again and inspect the tool entries. A tool can let Copilot perform an action, such as searching files, reading diagnostics, or running a terminal command, rather than only adding static context.

### 1.3 Chat Participants: `@`

Type `@` without submitting it. Participants route a request to a specialist with its own scope or capabilities. Depending on your installation, examples may include:

- `@workspace`: answer questions using the current workspace or codebase.
- `@vscode`: answer questions about VS Code features, settings, and commands.
- `@terminal`: help explain or construct shell commands.
- Extension-provided participants for services such as GitHub or Azure.

Try a workspace question if `@workspace` is available:

```text
@workspace Which file defines the application navigation, and which functions
render the three pages?
```

Expected answer: `app.py` defines navigation and calls `home.render()`, `calculator.render()`, and `codegen_lab.render()`.

If your VS Code version presents workspace context through Ask mode or `#codebase` instead of `@workspace`, run the same question with that option. This is a useful example of how Copilot features evolve while the underlying goal, grounding a question in the repository, stays the same.

### 1.4 Chat Modes

Open the mode picker and compare:

- **Ask**: learn, explain, and explore without requesting edits.
- **Plan**: develop and review an implementation approach before files change.
- **Agent**: edit files, use tools, run commands, and iterate toward a verified result.

What to notice:

- `/` selects a common task, `#` supplies context or a tool, and `@` selects a specialist.
- These features can be combined, such as asking a participant to explain a `#file`.
- Explicit context usually produces a more focused and verifiable answer.
- Ask mode is useful for learning without changing files.

## Step 2: Learn the Project

Stay in Ask mode for this step.

### 2.1 Get a Repository Overview

Submit this prompt:

```text
Summarize this Streamlit project for a developer who has not seen it before.
Identify the most impactful files, explain how page routing and shared styling
work, and describe how state moves through the calculator. Do not change files.
```

Open the files Copilot identifies and compare its summary with the running application. The expected high-impact files are:

- `app.py`, which configures the application and routes between pages.
- `views/calculator.py`, which owns calculator state and behavior.
- `views/codegen_lab.py`, which provides small Python examples for completion demos.
- `views/styles.py`, which injects shared CSS and renders the footer.

### 2.2 Explain Selected Code

1. Open `views/calculator.py`.
2. Select the entire `_evaluate` function.
3. Open inline chat or Copilot Chat and enter:

   ```text
   /explain
   ```

4. Ask this follow-up:

   ```text
   Why is the allowed-character check important, and what correctness or
   maintainability risks remain in this implementation?
   ```

What to notice: Copilot combines the selected code with repository context and can explain both behavior and tradeoffs.

### 2.3 Ask for Improvement Ideas

Submit this prompt:

```text
Review this repository and suggest three small, demonstrable improvements.
Prioritize correctness, tests, and maintainability. For each suggestion, name
the files likely to change and give one way to verify the result. Do not edit
anything yet.
```

Compare the suggestions with the predefined factorial exercise in Step 4.

## Step 3: Generate Code with Inline Completions

Goal: Generate a complete Python function from its signature, docstring, constants, and neighboring examples.

1. Open `views/codegen_lab.py`.
2. Place the following stub after `count_vowels` and before `find_max`:

   ```python
   def count_words(text: str) -> dict[str, int]:
       """Return case-insensitive word counts, ignoring punctuation."""
   ```

3. Press Enter after the docstring and wait for an inline suggestion.
4. Press `Tab` to accept a suitable suggestion, or press `Esc` and add a guiding comment if the suggestion does not meet the docstring.
5. In the `render` function, add a heading and text input for the new function. Start with this comment and let Copilot complete the surrounding Streamlit UI:

   ```python
   # Display case-insensitive word counts for text entered by the user.
   ```

6. Save the file, refresh the Code Generation Lab page, and try this input:

   ```text
   Copilot helps, and copilot explains.
   ```

Expected result: `copilot` has a count of `2`; punctuation is not included in any key.

Before moving on, check the generated code against the repository instructions:

- Function signatures have type hints.
- Public functions have concise docstrings.
- Repeated or special values are named constants when appropriate.
- Names follow `snake_case`.

## Step 4: Plan and Implement an Improvement

Mode: Plan

Session: Local

Goal: Plan a small, visible calculator feature, then let Agent mode implement and verify it.

The feature for this demo is a factorial calculator for non-negative integers. It should add one focused calculation helper and a small UI control to `views/calculator.py`.

Submit this prompt in Plan mode:

```text
Plan a small feature for views/calculator.py that computes the factorial of a
non-negative integer. Add a typed compute_factorial(number: int) -> int helper,
raise ValueError for negative input, and add a compact Streamlit control that
lets a user enter a whole number and see its factorial. Keep the existing
calculator behavior unchanged and follow .github/copilot-instructions.md. Keep
the change focused, preferably in this one file, and include validation steps.
Do not implement until I approve the plan.
```

Review the proposed plan before implementation:

1. Confirm that `0!` returns `1` and negative values are rejected.
2. Confirm that the UI accepts whole, non-negative numbers and displays the result clearly.
3. Ask Copilot to revise the plan if it proposes broad refactoring.
4. Answer any clarifying questions.
5. Select **Start Implementation** when the plan is ready.

During implementation, notice that:

- Copilot changes from Plan mode to Agent mode.
- A task list shows the implementation and validation progress.
- The agent can inspect files, edit code, run validation, and react to errors.
- You remain in control of proposed commands and file changes.

If Copilot needs a more concrete design direction, use this follow-up:

```text
Use an iterative implementation with a named factorial identity constant. Add a
number_input below the existing calculator keypad and display the result as
"n! = result". Do not change expression evaluation or calculator session state.
```

Suggested validation commands:

```powershell
python -m compileall app.py views
streamlit run app.py
```

Refresh the Calculator page and manually check:

- `0! = 1`
- `5! = 120`
- The control does not permit negative input.
- The original arithmetic keypad still works.

Expected result: Copilot completes a small planned change, validates the Python code, and leaves the existing calculator behavior intact.

## Step 5: Review and Commit the Changes

Goal: Treat AI-generated code like any other code: inspect it, validate it, and commit only what you understand.

### Option A: Review Selected Code

1. Select the generated `count_words` implementation or the calculator helper created in Step 4.
2. Right-click the selection and choose **Copilot > Review and Comment** if available.
3. Evaluate each suggestion against the requirements before accepting it.

### Option B: Review Working Tree Changes

1. Open Source Control from the Activity Bar.
2. In the Changes section, choose the Copilot code review action for uncommitted changes.
3. Inspect any inline comments and entries in the Problems panel.

### Option C: Ask for a Focused Review

Use Ask mode with this prompt:

```text
Review my uncommitted changes for functional bugs, factorial edge cases, UI
regressions, and violations of .github/copilot-instructions.md. Report findings
first, ordered by severity. Do not edit files.
```

### Manual Checks

Regardless of the review option, confirm that:

- `python -m compileall app.py views` passes.
- The Streamlit app still starts and all three pages render.
- The word-count example handles case and punctuation as requested.
- The factorial control produces `1` for `0` and `120` for `5`.
- No unrelated files or generated artifacts are included.

### Generate a Commit Message

1. Stage only the changes you intend to keep.
2. In Source Control, select **Generate Commit Message with Copilot**.
3. Edit the message so it accurately describes the final diff.
4. Commit the changes. Push or sync only if that is appropriate for your branch.

Success indicator: the new commit appears in the repository history and the working tree contains no unintended changes.

## Completion Checklist

- [ ] Browsed slash commands, context items or tools, participants, and chat modes.
- [ ] Asked Copilot for a repository overview.
- [ ] Explained selected calculator code with `/explain`.
- [ ] Generated a typed Python function with inline completion.
- [ ] Used Plan mode and Agent mode to add the factorial feature.
- [ ] Reviewed the generated code and ran validation.
- [ ] Generated, reviewed, and used a Copilot commit message.

## Optional Presenter Reset

To repeat the demo, discard only the demo changes or recreate the working branch from a known clean commit. Do not reset a working tree that contains changes you need to keep.
