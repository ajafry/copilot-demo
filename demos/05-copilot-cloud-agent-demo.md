# GitHub Copilot Cloud Agent Demo Script

**Target duration:** 15 to 20 minutes  
**Demo surfaces:** GitHub.com and Visual Studio Code  
**Repository:** `copilot-demo-dev-01`  
**Application:** Python 3.13+ Streamlit app

> **Presenter goal:** Delegate a real improvement in this repository to Copilot Cloud Agent, monitor its asynchronous work, review the resulting pull request, and request a focused revision.

---

## Demo outcome

Participants will see how to:

1. Turn an observed calculator behavior into a well-scoped GitHub issue.
2. Assign the issue to Copilot Cloud Agent.
3. Monitor the agent's progress and validation activity.
4. Review a small Python change and its pytest coverage.
5. Request an iteration through a pull request comment.
6. Choose between Cloud Agent and local agent mode in Visual Studio Code.

---

## Repository scenario

This repository contains a Streamlit application with three pages:

- **Home** presents GitHub Copilot features.
- **Calculator** implements an on-screen calculator in `views/calculator.py`.
- **Code Generation Lab** contains small Python exercises in `views/codegen_lab.py`.

The Calculator currently catches every evaluation failure and displays the generic result `Error`. During this demo, ask Cloud Agent to give division by zero a useful message while preserving existing behavior for other invalid expressions.

### Expected change

When a user evaluates `8 / 0`, the calculator should display:

```text
Cannot divide by zero.
```

The implementation should make the calculation behavior independently testable and add focused pytest tests.

### Why this task works well

- It is based on behavior that exists in the current repository.
- The result is visible in the Streamlit UI.
- The code change should remain limited to one application module and one test module.
- Acceptance criteria can be verified automatically.
- The repository's `.github/copilot-instructions.md` already defines Python, typing, constants, and pytest conventions.

---

## Prerequisites

Before presenting:

- Push this repository to GitHub and confirm the default branch is current.
- Confirm Copilot Cloud Agent is enabled for the account, organization, and repository.
- Confirm GitHub Actions and pull requests are available.
- Confirm you can assign issues to Copilot.
- Sign in to GitHub in the browser and GitHub Copilot in Visual Studio Code.
- Open this repository in Visual Studio Code.
- Verify the app starts with `streamlit run app.py`.
- Keep a completed run of this same task open in another tab in case the live agent takes too long.

### Recommended pre-demo check

1. Start the app with `streamlit run app.py`.
2. Open the **Calculator** page.
3. Enter `8 / 0` using the buttons.
4. Select `=` and verify the current display is `Error`.
5. Stop the app before the live demo if you do not need it running.

---

# Live demo script

## 0:00 to 2:00 | Show the application and frame the task

### On screen

Open the running Streamlit app, select **Calculator**, and evaluate `8 / 0`.

### Say

"This repository is a small Streamlit application used for GitHub Copilot demonstrations. Its calculator works for normal expressions, but division by zero produces only `Error`."

"We want a specific message that helps the user understand what happened. This is a bounded change with a visible outcome and an automated way to prove it works."

"I will delegate the implementation to Copilot Cloud Agent. It works asynchronously in a GitHub-hosted environment, so I can leave the session while it inspects the repository, edits files, and runs tests."

### Emphasize

- Local agent mode collaborates synchronously in the open VS Code workspace.
- Cloud Agent works asynchronously through GitHub and returns reviewable changes.
- A developer still reviews and approves the result.

---

## 2:00 to 5:00 | Create a repository-specific issue

### On screen

On GitHub.com, open **Issues**, select **New issue**, and enter the following content.

### Issue title

```text
Show a helpful message when the calculator divides by zero
```

### Issue body

```markdown
## Problem

The Streamlit calculator in `views/calculator.py` displays the generic result
`Error` when an expression divides by zero.

## Required behavior

When evaluation raises `ZeroDivisionError`, display exactly:

`Cannot divide by zero.`

Keep `Error` as the result for empty, invalid, or otherwise unsupported
expressions.

## Acceptance criteria

- Extract or introduce a small calculation function that can be tested without
	mutating Streamlit session state.
- Reuse named module-level constants for user-facing result strings.
- Add focused pytest tests for a valid expression, division by zero, and an
	invalid expression.
- Keep the UI layout and calculator controls unchanged.
- Do not add a new runtime dependency.
- Run the relevant tests and report the result.
```

Submit the issue.

### Say

"The issue is the task specification. It identifies the owning file, gives the exact user-visible result, defines behavior that must remain unchanged, and asks for focused tests."

"I have described the contract without prescribing the exact implementation. Copilot can inspect the current `_evaluate` path and follow the repository's established conventions."

### Teaching point

A strong agent task contains:

1. **Current behavior:** What happens now?
2. **Expected behavior:** What must change?
3. **Verification:** Which cases prove it?
4. **Boundaries:** What must remain untouched?

---

## 5:00 to 7:00 | Assign the issue to Copilot

### On screen

1. In the issue sidebar, open **Assignees**.
2. Select **Copilot**.
3. If an additional prompt is available, enter:

```text
Follow .github/copilot-instructions.md and keep the implementation focused on the calculator behavior and its tests.
```

4. If model selection is available, briefly show it and leave it on **Auto**.
5. Confirm the assignment.

### Say

"Assigning the issue starts a Cloud Agent session with the issue and repository as context. The agent can inspect the code, make changes on its branch, and run repository commands in its own environment."

"The repository instructions matter here. They require Python type hints, named constants instead of magic values, specific exception handling, and pytest naming conventions. Stable engineering guidance belongs there rather than in every issue."

---

## 7:00 to 9:30 | Monitor the agent session

### On screen

Open the repository's **Agents** experience and select the new session. Point out, when visible:

- The issue and repository context.
- The agent's plan or progress summary.
- Inspection of `views/calculator.py`.
- Files changed on the agent branch.
- Commands and test output.

If the live run is still working, switch to the prepared completed session.

### Say

"The session makes delegated work observable. I can see which code the agent inspected, what it changed, and how it validated the result."

"For this task, I expect it to find `_evaluate`, separate pure calculation behavior from Streamlit state mutation, and add a focused test module. If it chooses a different design, the acceptance criteria give me a concrete basis for review."

"I do not need to wait on this page. The task continues in the cloud while I work elsewhere."

### Optional steering prompt

Use this only if the agent starts changing unrelated UI code:

```text
Keep the Streamlit layout and button definitions unchanged. Limit the implementation to evaluation behavior and focused tests.
```

---

## 9:30 to 13:30 | Review the pull request

### On screen

Open the pull request produced by the completed session.

1. Read the title and summary.
2. Open **Files changed**.
3. Review the calculator implementation.
4. Review the new pytest tests.
5. Return to the conversation or session summary and show validation results.

### Review checklist

Confirm that the change:

- Returns `Cannot divide by zero.` only for `ZeroDivisionError`.
- Preserves `Error` for invalid input and other evaluation failures.
- Keeps Streamlit state handling in `_evaluate()` small.
- Uses named constants for result strings.
- Tests normal evaluation, division by zero, and invalid input.
- Does not alter `BUTTON_ROWS`, `OPERATOR_MAP`, or page layout.
- Does not add an unnecessary runtime package.

### Say

"Cloud Agent has produced a branch and pull request, not a silent change to the default branch. I am reviewing both the implementation and the evidence."

"The important design point is testability. The expression calculation should be callable without constructing Streamlit session state, while `_evaluate` remains responsible for putting the result into the UI state."

"The tests are part of the deliverable. I want to see the exact zero-division message and proof that existing valid and invalid cases still behave correctly."

---

## 13:30 to 15:30 | Request a focused iteration

### On screen

Add a line comment to a test, or a general pull request comment:

```text
@copilot Please add a focused test for the existing empty-expression behavior. Keep the implementation unchanged unless that test exposes a defect, then run the calculator tests again and summarize the result.
```

### Say

"Review comments continue the agent workflow. This request is specific: add one missing boundary test, avoid unrelated implementation churn, and rerun the narrow test suite."

"For multiple related findings, I would submit them together as a review so Copilot receives one coherent revision request. The updated commits and validation remain visible on the same pull request."

If mentions are unavailable, show the prepared comment without submitting it and explain that repository policy controls this capability.

---

## 15:30 to 17:30 | Compare Cloud Agent with local agent mode

### On screen

Switch to this repository in Visual Studio Code and open Copilot Chat. Show `views/calculator.py` and the repository context, but do not start another implementation.

### Say

"The same repository is open locally, but the collaboration model is different."

"I use local agent mode when I want an interactive partner that edits this workspace while I watch, answer questions, or inspect the running app. I use Cloud Agent when the task is well-bounded and I want it to run asynchronously through the GitHub workflow."

"A useful rule is: pair locally when the work needs frequent interaction; delegate to the cloud when the task has clear acceptance criteria and can be reviewed later."

### Optional read-only prompt to display

```text
Explain how calculator expressions flow from a button click to the displayed result. Do not make changes.
```

---

## 17:30 to 19:00 | Close and show adjacent entry points

### On screen

Return to the pull request or Agents page.

### Say

"We observed a real behavior in this Streamlit app, wrote a bounded issue, delegated it to Copilot Cloud Agent, monitored the session, reviewed code and tests, and requested a follow-up change."

"Cloud Agent can also be started from supported agent prompt boxes, integrations, and failing GitHub Actions workflows. Those entry points use the same core pattern: give the agent a clear task, inspect its work, and review the proposed change before merging."

"The value is not only code generation. It is asynchronous implementation that remains connected to issues, branches, pull requests, tests, comments, and human approval."

---

# Presenter quick reference

## Key repository files

| File | Purpose in the demo |
|---|---|
| `app.py` | Configures the Streamlit app and routes to the Calculator page. |
| `views/calculator.py` | Owns calculator buttons, expression state, and evaluation. |
| `.github/copilot-instructions.md` | Defines Python style, constants, typing, and pytest conventions. |
| `requirements.txt` | Declares Streamlit; the task should not add a runtime dependency. |
| `tests/test_calculator.py` | Expected focused test file created by the agent. |

## Expected validation commands

The agent may choose environment-appropriate equivalents, but the review should show a focused test command such as:

```powershell
python -m pytest tests/test_calculator.py
```

For a manual UI check:

```powershell
streamlit run app.py
```

## Expected manual verification

| Expression | Expected display |
|---|---|
| `8 / 2` | `4.0` |
| `8 / 0` | `Cannot divide by zero.` |
| Empty expression | `Error` after evaluation |
| Unsupported input | `Error` |

## Good follow-up prompts

```text
Explain why you chose this function boundary before making more changes.
```

```text
Keep the tests independent of Streamlit session state.
```

```text
Do not change the calculator controls or visual layout.
```

```text
Run only the calculator tests and summarize the cases covered.
```

---

# Contingency plan

## Copilot is not available as an assignee

Say:

"Cloud Agent availability is controlled by account, organization, and repository policy. I will use a prepared session of the same calculator task so we can still review the workflow."

Open the prepared session or pull request.

## The live session is still running

Say:

"The task is asynchronous, so I will let this session continue and switch to a completed run of the same issue."

Do not wait silently for completion.

## The agent changes too much

Use this as a review moment:

```text
@copilot Revert the unrelated UI changes. Keep this pull request limited to calculator evaluation behavior and its tests.
```

## Tests cannot run because pytest is unavailable

Ask the agent to add pytest as a development dependency using the repository's existing dependency-management pattern. Do not add it as an application runtime requirement merely to make the demo pass.

## The implementation differs from the expected design

Evaluate it against the acceptance criteria rather than presentation expectations. If behavior, testability, scope, and validation are sound, explain that agents can reach a valid result through different implementations.

## The Agents tab is not visible

Use the global agents panel or the prepared pull request. Explain that labels and entry points vary with GitHub product availability and repository policy.

---

# Before presenting

- Recheck the current GitHub labels and Cloud Agent entry points in your tenant.
- Confirm the issue text still matches `views/calculator.py`.
- Prepare one completed pull request from the exact issue in this script.
- Confirm the completed pull request includes passing calculator tests.
- Keep the running Streamlit app and prepared pull request in separate tabs.
- Avoid showing credentials, tokens, private endpoints, or sensitive repository content.
