# Customize GitHub Copilot Demo

Use this demo to tailor GitHub Copilot for the Streamlit application in this repository. It follows the major topics in the [source customization guide](https://github.com/ps-copilot-sandbox/copilot-intermediate-gallery-repo/blob/main/demos/customize-copilot.md), but uses current VS Code customization formats and simple exercises based on this codebase.

Documentation and UI guidance in this script was checked on August 5, 2026. Features can vary by VS Code version, Copilot plan, organization policy, and enabled extensions.

## What You Will Learn

By the end of this demo, you will be able to:

- Inspect Copilot usage information and available models.
- Compare two models with the same small, read-only task.
- Create a workspace prompt file and invoke it as a slash command.
- Create a workspace custom agent and use it from the agent picker.
- Understand how repository instructions affect generated answers.
- Configure the official remote GitHub MCP server with OAuth.
- Safely create and verify a GitHub issue through MCP tools.
- Choose between instructions, prompts, agents, skills, and MCP.

Estimated time: 25-30 minutes.

## Before the Demo

1. Open this repository in VS Code and sign in to GitHub Copilot.
2. Use VS Code 1.101 or later for remote MCP and OAuth support.
3. Confirm that Agent mode and MCP servers are permitted by your organization.
4. Use a branch or working tree where creating workspace customization files is acceptable.
5. Confirm the repository remote:

   ```powershell
   git remote get-url origin
   ```

   This demo targets `ajafry/copilot-demo-dev-01`.

6. Open Copilot Chat in a Local session.

The MCP exercise creates a real GitHub issue. Use a repository where you have permission, review the draft, and obtain any required approval before performing the write.

## Step 1: Inspect Usage and Model Availability

### 1.1 View Usage in VS Code

1. Open the Copilot status dashboard from the VS Code Status Bar.
2. Review the percentage of your monthly AI credit allowance used. Copilot Free also shows inline-suggestion usage.
3. Hover over a chat response to see the AI credits used by that request.
4. Hover over or select the context window control in the chat input to see cumulative credits and token usage for the session.

The exact counters depend on your current plan and billing model. Current plans use AI credits. GitHub's older "premium request" documentation applies only to some legacy annual Copilot Pro and Pro+ subscriptions, so do not present premium requests as the current universal billing unit.

For account-level billing information, open [GitHub Billing and licensing](https://github.com/settings/billing). Depending on the account, relevant information may appear under metered usage, AI credits, Copilot, or usage analytics.

Discussion:

- Which models or features show a usage cost in the model picker?
- Is the account managed personally or by an organization?
- Does organization policy restrict models, agents, or MCP?

### 1.2 Inspect the Model Picker

1. Open the model picker in the Copilot Chat input area.
2. Notice **Auto**, available models, and any cost or capability details.
3. Select **Manage Language Models** to inspect model capabilities, context size, billing details, and visibility.

The available list changes over time. Business and Enterprise administrators can restrict model access, and Restricted Mode may show only **Auto**.

## Step 2: Compare Models with One Prompt

Session target: Local. Built-in role: Agent.

Use a read-only task so model comparison does not produce competing file edits.

1. Attach `views/calculator.py` with **Add Context**, or type `#` and select the file from the context picker.
2. Select one available model.
3. Submit this prompt:

   ```text
   Review the _backspace function in views/calculator.py. In exactly three
   bullets, explain what it does, name one edge case, and suggest one small
   improvement. Do not edit files.
   ```

4. Save the answer in your notes.
5. Start a new chat, select another available model, attach the same file, and submit the identical prompt.
6. Compare factual accuracy, instruction following, concision, and usefulness.

What to notice:

- A longer answer is not automatically a better answer.
- A model recommendation is still a proposal that must match the actual code.
- Chat model selection does not automatically change the inline-suggestions model.
- **Auto** can choose a model based on task and availability; hover over the response to inspect the model used when supported.

## Step 3: Create and Use a Prompt File

Prompt files encode one repeatable task. Workspace prompt files live in `.github/prompts/` and use the `.prompt.md` extension.

This repository already contains `.github/prompts/create-factorial.prompt.md`. Open it briefly to see a working example, then create a new prompt so the demo shows the full lifecycle.

### 3.1 Create the Prompt

1. In a Local session, select the **Agent** role.
2. Type the following command in Chat:

   ```text
   /create-prompt Create a read-only workspace prompt named
   review-python-function that reviews one Python function for correctness and
   repository conventions.
   ```

3. Answer any clarifying questions and choose **Workspace** when asked where to save the prompt.
4. Confirm that Copilot creates `.github/prompts/review-python-function.prompt.md`.
5. Open the generated file and replace its contents with this deterministic version:

   ```markdown
   ---
   name: "review-python-function"
   description: "Review one Python function for correctness and repository conventions"
   argument-hint: "Function name or selected Python code"
   ---
   Review the Python function named or selected by the user.

   Use [the repository instructions](../copilot-instructions.md) as the standard.

   Return exactly these sections:
   1. **Behavior**: one sentence describing what the function does.
   2. **Findings**: up to three correctness or convention issues, each with evidence.
   3. **Check**: one inexpensive way to verify the most important finding.

   Do not edit files. Do not invent a finding when the code is already correct.
   ```

6. Save the file.

The frontmatter gives the prompt a predictable slash-command name, description, and input hint. Because it does not specify an `agent` or `tools` value, it uses the currently selected agent and that agent's tools. The relative Markdown link reuses project instructions instead of duplicating them.

`/create-prompt` generates a prompt file with AI assistance. By contrast, `/prompts` only opens the **Configure Prompt Files** menu. To create a blank prompt manually, use **Chat: New Prompt File** or the Agent Customizations editor.

### 3.2 Use the Prompt

1. Open `views/calculator.py` and select the `_evaluate` function.
2. Open Copilot Chat and type `/`.
3. Select or enter:

   ```text
   /review-python-function _evaluate in views/calculator.py
   ```

4. Submit the prompt.

Expected result: the response uses the three requested sections, discusses the selected function, cites actual evidence, and does not edit files.

Alternative ways to run it:

- Run **Chat: Run Prompt** from the Command Palette.
- Open the prompt file and select the play button in the editor title.

If the prompt does not appear, confirm the `.prompt.md` extension and `.github/prompts/` location, then open Chat diagnostics or agent debug logs to check discovery errors.

## Step 4: Create and Use a Custom Agent

Custom agents define a reusable role, its behavior, and its available tools. This exercise creates a deliberately read-only reviewer.

### 4.1 Create the Agent

1. In a Local session, select the **Agent** role.
2. Type the following command in Chat:

   ```text
   /create-agent Create a workspace custom agent named Streamlit Reviewer. It
   must review this Streamlit app using only read and search tools, and it must
   never edit files or run commands.
   ```

3. Answer any clarifying questions.
4. Ensure that the generated workspace agent is saved as `.github/agents/streamlit-reviewer.agent.md`.
5. Open the generated file and replace its contents with this deterministic version:

   ```markdown
   ---
   name: "Streamlit Reviewer"
   description: "Use when reviewing this Streamlit app for simple correctness and maintainability issues"
   argument-hint: "File, function, or behavior to review"
   tools: [read, search]
   user-invocable: true
   ---
   You are a read-only reviewer for this Streamlit application.

   Follow [the repository instructions](../copilot-instructions.md).

   - Inspect only the files needed for the requested review.
   - Prioritize functional bugs and regressions over style preferences.
   - Cite the file and function that support each finding.
   - Give one simple verification step for each finding.
   - Do not edit files or run commands.
   - If no issue is found, say so clearly.

   Return findings first, ordered by severity, followed by a short summary.
   ```

6. Save the file.

What to notice: `tools: [read, search]` applies least privilege. The agent can inspect the repository but cannot edit files or execute terminal commands.

`/create-agent` generates a custom agent with AI assistance. By contrast, `/agents` only opens the **Configure Custom Agents** menu. To create a blank agent manually, use **Chat: New Custom Agent** or the Agent Customizations editor.

### 4.2 Use the Agent

1. Start a new local chat.
2. Open the agent picker next to the chat input.
3. Select **Streamlit Reviewer**.
4. Submit:

   ```text
   Review views/calculator.py. Focus only on how expression input is validated
   and evaluated. Do not suggest unrelated UI changes.
   ```

Expected result: the response follows the reviewer format and no files change.

Confirm the tool boundary:

```text
Now change the calculator implementation and run it.
```

The custom agent should explain that editing and execution are outside its role or unavailable with its tools. Switch back to a built-in Agent only when implementation is actually intended.

If the agent is missing from the picker, run **Chat: Open Customizations**, inspect the Agents tab, and verify the file location and YAML frontmatter.

## Step 5: Understand Repository Instructions

This repository already has `.github/copilot-instructions.md`; do not generate a replacement during the demo.

1. Open `.github/copilot-instructions.md`.
2. Identify rules for type hints, docstrings, constants, exceptions, imports, and pytest.
3. In a Local chat, attach `.github/copilot-instructions.md` with **Add Context**, or type `#` and select the file from the context picker.
4. Submit:

   ```text
   List the five attached repository-instruction rules most relevant to adding a
   new public Python calculation function. Do not edit files.
   ```

5. Compare the answer with the prompt and agent created in Steps 3 and 4.

Use the right customization for the job:

| Need | Customization |
| --- | --- |
| Always-on project conventions | `.github/copilot-instructions.md` |
| Rules for selected file patterns | `.github/instructions/*.instructions.md` |
| One reusable, focused task | `.github/prompts/*.prompt.md` |
| A reusable role with restricted tools | `.github/agents/*.agent.md` |
| A multi-step workflow with scripts or templates | Agent skill |
| Access to an external service or API | MCP server |

Keep instructions concise and testable. Avoid placing a one-time task in always-on instructions because it consumes context on unrelated requests.

## Step 6: Configure the GitHub MCP Server

The official GitHub MCP Server exposes GitHub repositories, issues, pull requests, users, and other capabilities as agent tools. Its default toolsets are `context`, `repos`, `issues`, `pull_requests`, and `users`. This demo uses only the context, repository, user, and issue capabilities.

### 6.1 Add the Official Remote Server

Preferred guided flow:

1. Press `Ctrl+Shift+P` and run **MCP: Add Server**.
2. Choose **HTTP**.
3. Enter the official remote endpoint:

   ```text
   https://api.githubcopilot.com/mcp/
   ```

4. Name the server `github`.
5. Choose **Workspace** if the team should share the server definition, or **Global** if it is only for your user profile.
6. Review the generated configuration before starting the server.
7. Confirm that you trust the official server configuration.
8. Complete the browser-based GitHub OAuth flow when prompted.

The equivalent workspace configuration is:

```json
{
  "servers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    }
  }
}
```

Workspace configuration is normally stored in `.vscode/mcp.json`. Do not add a PAT or other secret to this file. OAuth avoids creating and committing a token.

### 6.2 Verify the Server and Tools

1. Run **MCP: List Servers** and select `github`.
2. Confirm that the server is running; use **Show Output** if startup or authentication fails.
3. Open Agent mode and select **Configure Tools** in the chat input.
4. Find the GitHub server and confirm that tools from the `context`, `repos`, `users`, and `issues` toolsets are enabled.

If issue write tools are absent, check organization MCP policy, repository permissions, server authentication, and whether the server is configured read-only. Do not broaden permissions merely to finish a demo.

## Step 7: Create a GitHub Issue with MCP

This exercise has two phases: prepare and review the draft, then explicitly authorize the write.

### 7.1 Draft and Check for Duplicates

In Agent mode, submit:

```text
Use only the GitHub MCP server for this task.

For repository ajafry/copilot-demo-dev-01:
1. Identify the currently authenticated GitHub user.
2. Search open issues for duplicates about calculator history.
3. Draft, but do not create, an issue titled
   "[Demo] Add calculator history panel".
4. The body should explain the user value and contain these acceptance criteria:
   - Show the five most recent successful calculations.
   - Provide a Clear history action.
   - Keep history only for the current Streamlit session.
5. Do not add labels, assignees, milestones, or projects unless you first verify
   that they exist and I explicitly request them.

Stop and show me the target repository, authenticated user, duplicate-search
result, title, and complete body. Wait for my confirmation before creating it.
```

Review the draft carefully:

- Is the target exactly `ajafry/copilot-demo-dev-01`?
- Is the authenticated account expected and authorized?
- Did the duplicate search complete?
- Is the issue clearly marked as a demo?
- Does the body contain only intended information?

If a duplicate exists, do not create another issue. Use the existing issue for discussion or choose a different demonstration topic.

### 7.2 Confirm the Write

Only after the draft is correct, submit:

```text
Create that issue exactly as drafted using the GitHub MCP server. After creation,
return the issue number and URL. Do not make any other GitHub changes.
```

Approve the issue-creation tool call when VS Code asks. Do not enable blanket approval for unrelated GitHub write tools.

Expected result: Agent mode returns a new issue number and URL. Open the URL and verify the title, body, repository, and author.

### 7.3 Clean Up the Demo Issue

If the issue is only a temporary demonstration, close it on GitHub with a clear comment, or ask Agent mode to close that exact issue through GitHub MCP. Review and approve the close operation separately. Use an appropriate close reason; a demonstration issue that will not be implemented should normally be closed as **not planned**.

## Optional Step 8: Compare Prompt and Agent Scope

Run the prompt from Step 3 while **Streamlit Reviewer** is selected:

```text
/review-python-function _append in views/calculator.py
```

Observe the interaction between the prompt and agent:

- The prompt defines the immediate output structure.
- The custom agent defines the reviewer role and available tools.
- Repository instructions supply shared Python conventions.
- Because this prompt does not set `agent` or `tools`, the selected **Streamlit Reviewer** agent supplies both.
- If a prompt specifies tools, its tool list takes precedence over the selected agent's tools.

This layering is useful, but unnecessary duplication makes customization harder to maintain. Keep each concern in one appropriate place.

## Completion Checklist

- [ ] Inspected current usage information without assuming legacy billing applies.
- [ ] Compared two available models with the same read-only prompt.
- [ ] Created `review-python-function.prompt.md`.
- [ ] Invoked the new prompt with `/review-python-function`.
- [ ] Created `streamlit-reviewer.agent.md` with read-only tools.
- [ ] Selected and used the new custom agent.
- [ ] Reviewed the repository's existing Copilot instructions.
- [ ] Configured or inspected the official GitHub MCP server with OAuth.
- [ ] Verified the GitHub identity, repository, and duplicate search before writing.
- [ ] Explicitly approved and verified a GitHub issue creation, or stopped safely before the write.

## Cleanup

1. Close the demonstration issue if it should not remain open.
2. Delete the prompt or agent files only if they should not become shared repository customizations.
3. Remove a workspace MCP configuration if the repository should not advertise that server.
4. Use **MCP: List Servers** to stop or disable the GitHub server when it is no longer needed.
5. Review `git status` and keep only intentional files.

## Current References

- [Prompt files in VS Code](https://code.visualstudio.com/docs/agent-customization/prompt-files)
- [Custom agents in VS Code](https://code.visualstudio.com/docs/agent-customization/custom-agents)
- [Custom instructions in VS Code](https://code.visualstudio.com/docs/agent-customization/custom-instructions)
- [MCP servers in VS Code](https://code.visualstudio.com/docs/agent-customization/mcp-servers)
- [Official GitHub MCP Server](https://github.com/github/github-mcp-server)
- [AI language models in VS Code](https://code.visualstudio.com/docs/agent-customization/language-models)
- [Agent harnesses in VS Code](https://code.visualstudio.com/docs/agents/run/agent-harnesses)
- [AI features in VS Code cheat sheet](https://code.visualstudio.com/docs/agents/reference/ai-features-cheat-sheet)
- [Optimize AI credit usage in VS Code](https://code.visualstudio.com/docs/agents/guides/optimize-usage)

## Related Demos

- [GitHub Copilot Features Demo](./01-features-demo.md)
- [GitHub Copilot Engineering Practices Demo](./02-engineering-practices-demo.md)