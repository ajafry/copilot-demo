# GitHub Copilot Engineering Practices Demo

Use this demo to explore professional practices for understanding, sharing, governing, and verifying GitHub Copilot interactions. It follows the topic coverage of the [source engineering-practices guide](https://github.com/ps-copilot-sandbox/copilot-intermediate-gallery-repo/blob/main/demos/engineering-practices.md), with exercises grounded in this repository's Streamlit calculator application.

## What You Will Learn

By the end of this demo, you will be able to:

- Inspect the prompts, instructions, context, tools, and response details used by Copilot.
- Diagnose why a Copilot answer may be incomplete or unexpected.
- Export and import IDE chat conversations for team collaboration.
- Distinguish system, personal, repository, and prompt-level instructions.
- Create grounded prompts that produce more verifiable answers.
- Use currently documented GitHub.com conversation history and subthreads.
- Apply review and validation practices before accepting AI-generated work.

Estimated time: 15-20 minutes.

## Before the Demo

1. Open this repository in VS Code and sign in to GitHub Copilot.
2. Confirm that the application dependencies are installed in the active virtual environment:

   ```powershell
   python -m pip install -r requirements.txt
   ```

3. Start the application in a terminal:

   ```powershell
   streamlit run app.py
   ```

4. Open the local URL shown by Streamlit, normally `http://localhost:8501`.
5. Open Copilot Chat and select Ask mode with a Local session.

Some command names and GitHub.com controls vary by Copilot plan, VS Code version, organization policy, and installed extensions. Use the options visible in your environment when a label differs.

## Part 1: Inspect Copilot in VS Code

### Step 1: Generate a Traceable Interaction

Why this matters: a short, repository-specific interaction makes it easier to see which context Copilot used and which assumptions it made.

1. Open `views/calculator.py`.
2. Select the `_evaluate` function.
3. Submit this prompt in Ask mode:

   ```text
   Explain #selection. Identify its inputs, state changes, validation behavior,
   and two edge cases. Do not change any files.
   ```

4. Keep the response open for the next step.

Expected result: the answer should mention Streamlit session state, the allowed-character check, expression evaluation, and the `Error` result.

### Step 2: Open Copilot Chat Diagnostics

Why this matters: when a result is surprising, diagnostics help separate a prompt problem from missing context, conflicting instructions, or tool behavior.

#### Open the Diagnostic View

1. Press `Ctrl+Shift+P` on Windows or Linux, or `Cmd+Shift+P` on macOS.
2. Search for `Copilot Chat Debug`.
3. Select **Copilot Chat Debug: Focus on Copilot Chat Debug View** or the closest available diagnostic command.

You can also open the Command Palette from **View > Command Palette**.

If the command is unavailable, open **View > Output** and choose a GitHub Copilot or GitHub Copilot Chat channel. Output logs are less detailed but can still expose requests, tool calls, and failures.

#### Inspect the Interaction

Select the most recent request and look for:

- **User prompt**: the instruction you submitted.
- **System instructions**: baseline rules that shape Copilot's behavior.
- **Repository instructions**: project guidance loaded from `.github/copilot-instructions.md`.
- **Context**: selected code, files, workspace data, diagnostics, or terminal content.
- **Tools**: searches, file reads, terminal commands, or other actions invoked for the request.
- **Model and request metadata**: model selection, timing, token usage, session details, or request identifiers when exposed.
- **Response details**: the generated answer and any tool results that informed it.

Ask these diagnostic questions:

1. Was the selected `_evaluate` function included?
2. Was `views/calculator.py` attached as a file or only as a selection?
3. Were the repository instructions present?
4. Did Copilot use a search or file-reading tool?
5. Can every claim in the response be traced to visible context?

Do not publish raw diagnostic data without reviewing it. Logs can contain source code, file paths, repository details, prompts, and other sensitive context.

### Step 3: Compare Ungrounded and Grounded Prompts

Why this matters: explicit evidence makes an answer easier to review and reduces accidental assumptions.

Start a new Ask-mode chat and submit this broad prompt:

```text
How does the calculator work, and what should be improved?
```

Then submit this grounded version:

```text
Using #file:views/calculator.py and #file:.github/copilot-instructions.md,
explain how calculator input reaches the display. Suggest exactly two small
improvements, cite the function each affects, and give one verification step
per improvement. Do not edit files.
```

If your client inserts file references through a picker, type `#`, choose **File**, and select each file rather than typing the reference literally.

Compare the responses:

- Does the grounded answer use actual function and constant names?
- Are its suggestions compatible with the repository's Python conventions?
- Does it distinguish facts from recommendations?
- Are its verification steps specific enough to run?

Engineering practice: provide the smallest context set that contains the controlling code, its constraints, and a way to verify the answer.

## Part 2: Share IDE Chat Conversations

### Step 4: Export a Chat

Why this matters: an exported conversation preserves useful prompts and reasoning for review, handoff, or troubleshooting.

1. Return to the grounded conversation from Step 3.
2. Press `Ctrl+Shift+P` or `Cmd+Shift+P`.
3. Search for `Chat: Export`.
4. Select **Chat: Export Chat...**.
5. Save the export outside the repository unless it is intentionally approved project documentation.

Before sharing an export, review it for:

- Secrets, credentials, access tokens, and connection strings.
- Customer, employee, or other personal data.
- Proprietary code or internal repository names.
- Absolute paths, machine names, and terminal output.
- Incorrect conclusions that a teammate might mistake for verified facts.

Add a short handoff note when sharing:

```text
Purpose: Compare grounded and ungrounded repository analysis.
Verified: File references and compile command were checked locally.
Not verified: Suggested improvements were not implemented.
Environment: VS Code and Copilot versions may change menu labels.
```

### Step 5: Import a Chat

1. Press `Ctrl+Shift+P` or `Cmd+Shift+P`.
2. Search for `Chat: Import`.
3. Select **Chat: Import Chat...**.
4. Choose a trusted exported chat file.
5. Confirm which parts are historical transcript and which context must be reattached in the current workspace.

Important: an imported conversation is evidence of a prior interaction, not proof that its code, dependencies, branch, or diagnostics still match the current checkout.

A useful team review asks:

- What prompt pattern worked well?
- Which files or selections made the answer precise?
- What was validated by tools or tests?
- Which assumptions should not be reused?

## Part 3: Understand the Instruction Hierarchy

### Step 6: Inspect Repository Instructions

Why this matters: shared repository instructions encode engineering expectations once, so every contributor does not need to repeat them in every prompt.

Open `.github/copilot-instructions.md` and identify the rules for:

- Python version and formatting.
- Type hints and docstrings.
- Constants and magic values.
- Imports, exceptions, and file-system APIs.
- pytest naming and test independence.

Now ask Copilot:

```text
Using #file:.github/copilot-instructions.md, review #file:views/calculator.py
against this repository's standards. Return a table with Rule, Evidence,
Status, and Minimal Fix. Do not edit files and do not invent violations.
```

What to notice:

- **System instructions** define baseline behavior and platform constraints.
- **Personal instructions** express preferences that follow an individual where supported.
- **Repository instructions** define shared project conventions for this codebase.
- **Prompt instructions** define the immediate task, scope, and expected output.
- More specific instructions usually refine broader ones, but cannot override safety, access, or platform constraints.

Good instructions are specific, testable, concise, and relevant to repeated work. Avoid vague guidance such as "write good code" when a rule like "use type hints on all function signatures" is verifiable.

### Step 7: Explore Personal Instructions on GitHub.com

1. Open [GitHub Copilot](https://github.com/copilot).
2. Open the user menu, normally in the lower-left area.
3. Select **Personal instructions** or the closest available settings entry.
4. Read the existing instructions before changing anything.

For a temporary demonstration, add a harmless and observable preference such as:

```text
When explaining Python code, begin with a one-sentence summary and then list
inputs, outputs, side effects, and edge cases.
```

Test it with:

```text
Explain a Python function that computes factorial iteratively.
```

After the demonstration, remove the temporary instruction if it should not affect future chats.

Do not place repository-specific secrets, internal URLs, or sensitive customer context in personal instructions. Use repository instructions for stable project conventions and task prompts for temporary requirements.

## Part 4: Use Current GitHub.com Conversation Features

Documentation check: as of August 5, 2026, the current [GitHub Docs for Copilot Chat on GitHub.com](https://docs.github.com/en/copilot/how-tos/chat-with-copilot/chat-in-github) do not document a conversation-level **Share** button or a **Manage shared conversations** page. Those controls appeared in the older source demo but should not be expected in the current interface. Use the VS Code export/import workflow in Steps 4 and 5 when a transcript must be shared.

### Step 8: Inspect Conversation History and Retention

Why this matters: GitHub.com retains a limited conversation history, so chat should not be treated as permanent project documentation.

1. Open [GitHub Copilot](https://github.com/copilot) and start a new conversation.
2. Submit this prompt:

   ```text
   What engineering checks should a team apply before accepting an AI-generated
   Python factorial function? Cover correctness, edge cases, type contracts,
   tests, readability, and security in no more than eight bullets.
   ```

3. Start another new conversation, then inspect the conversation-history list.
4. Reopen the factorial conversation and confirm that its prior messages are present.
5. Delete the demonstration conversation if you do not need to retain it.

Current GitHub Docs state that Copilot Chat stores up to 100 recent conversations and permanently deletes messages after 28 days. Once no messages remain, the empty conversation is removed from history. Retention behavior can change, so verify the current documentation before using it in governance or compliance guidance.

Engineering practice: move durable decisions into an approved artifact such as an issue, pull request, decision record, or repository document. A chat-history entry is temporary working context, not a system of record.

### Step 9: Explore a Decision with Subthreads

Why this matters: subthreads let you compare alternatives without losing the original line of reasoning.

1. Reopen the factorial conversation or create it again if it was deleted.
2. Hover over the original prompt and select the edit control.
3. Change the prompt to request a recursive implementation:

   ```text
   What engineering checks should a team apply before accepting a recursive
   Python factorial function? Include recursion depth and input-size risks.
   ```

4. Submit the edited prompt. GitHub.com displays the response in a new subthread.
5. Use the edit counter and navigation controls beneath the prompt to compare the original and edited branches.

Discuss which branch is better supported by evidence and which implementation better fits this repository. Preserve the decision and its rationale outside chat if the team intends to rely on it.

## Part 5: Verify AI-Assisted Work

### Step 10: Use a Review-and-Validation Loop

Copilot output is a proposal until it is reviewed and verified. Demonstrate the loop with a read-only review of the calculator:

```text
Review #file:views/calculator.py for correctness and violations of
#file:.github/copilot-instructions.md. Findings must include file evidence and a
specific verification step. Report findings first, ordered by severity. Do not
edit files.
```

Evaluate the answer using this sequence:

1. **Scope**: Did Copilot stay within the requested files and concerns?
2. **Evidence**: Can each finding be tied to actual code?
3. **Risk**: Is severity based on realistic impact rather than style preference?
4. **Change**: Is the proposed fix minimal and compatible with existing behavior?
5. **Validation**: Is there an executable or observable check?

Run the repository's inexpensive baseline validation:

```powershell
python -m compileall app.py views
```

Then use the running Streamlit app for behavioral checks:

- Navigate among Home, Calculator, and Code Generation Lab.
- Evaluate `2 + 3 * 4` and confirm the result is `14`.
- Try malformed input through the available controls and confirm the page remains usable.
- Confirm that no review exercise changed the working tree unexpectedly.

Useful additions for a production repository include automated tests, formatting, linting, type checking, dependency scanning, secret scanning, and pull-request review. The appropriate checks should be documented and runnable, not assumed from Copilot's confidence.

## Presenter Discussion Prompts

Use any of these to extend the demo:

- When is a diagnostic trace more useful than simply rephrasing a prompt?
- What information should never be placed in a shared chat?
- Which standards belong in personal instructions versus repository instructions?
- When should a useful chat conclusion be promoted into an issue, pull request, or decision record?
- What is the cheapest check that could prove a Copilot recommendation wrong?
- Which decisions still require a human owner even when Agent mode performs the implementation?

## Completion Checklist

- [ ] Generated and inspected a repository-grounded Copilot interaction.
- [ ] Opened Copilot diagnostics or an available Copilot output channel.
- [ ] Compared an ungrounded prompt with an explicitly grounded prompt.
- [ ] Exported a chat and reviewed it for sensitive information.
- [ ] Imported or discussed how to safely reuse a shared chat.
- [ ] Inspected `.github/copilot-instructions.md` and the instruction hierarchy.
- [ ] Explored personal instructions on GitHub.com.
- [ ] Inspected GitHub.com conversation history and current retention guidance.
- [ ] Created or reviewed a GitHub.com Copilot subthread.
- [ ] Applied an evidence, review, and validation loop to Copilot output.

## Cleanup

1. Remove any temporary personal instruction added during the demo.
2. Delete demonstration conversations and local chat exports you do not need to retain.
3. Stop the Streamlit server with `Ctrl+C` when the application is no longer needed.
4. Confirm that the working tree contains no unintended demo changes.

## Related Demo

Complete the introductory workflow first or revisit it for feature fundamentals: [GitHub Copilot Features Demo](./features-demo.md).