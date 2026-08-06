# GitHub Copilot Spaces Demo

Use this short demo to show the difference between asking GitHub Copilot a question without curated context and asking the same question in a Copilot Space.

This exercise is based on the [source Spaces demo](https://github.com/ps-copilot-sandbox/copilot-intermediate-gallery-repo/blob/main/demos/copilot-spaces.md), simplified for this Streamlit repository. Documentation and UI guidance were checked on August 5, 2026.

## What You Will Learn

- See how Copilot responds when important repository context is missing.
- Create a Space with two relevant repository files and focused instructions.
- Compare the same prompt with and without the Space.

Estimated time: 10 minutes.

## Before the Demo

1. Sign in to GitHub with an account that can access `ajafry/copilot-demo-dev-01`.
2. Confirm that GitHub Copilot Chat and Copilot Spaces are available for the account.
3. If the model picker is available, use the same model for both prompts so that the Space is the main variable.

## Step 1: Ask Without a Space

This step is performed in a web browser, not in VS Code. Copilot Chat is available from any page on GitHub, but the direct `https://github.com/copilot` URL opens the standalone chat page. It does not automatically attach this repository.

1. Open a new browser tab and go directly to [GitHub Copilot Chat](https://github.com/copilot).
2. Sign in if prompted.
3. Confirm that the address bar shows `github.com/copilot`, not a repository URL such as `github.com/ajafry/copilot-demo-dev-01` and not a Space URL under `github.com/copilot/spaces`.
4. Use a fresh conversation so an earlier thread does not supply repository context.
5. Do not add an attachment or select a repository, file, issue, pull request, or Space as context.
6. Enter the prompt in the prompt box and press **Enter**:

   ```text
   You are planning a small improvement to the calculator in this repository.
   What are the two most important code-quality risks in its expression
   evaluation, and which repository coding rules should guide the fix?
   Cite the files and functions that support your answer. Do not write code.
   ```

7. Keep the response open or save it for comparison.

What to notice:

- Copilot may ask which repository you mean.
- It may give generic calculator advice rather than discuss this implementation.
- It cannot reliably cite the relevant files, functions, and repository rules without that context.

Do not correct the prompt or attach files. The missing context is the point of this first run.

If you open Copilot Chat while viewing a repository page, GitHub can provide that page as relevant context. That is useful normally, but avoid it for this comparison because Step 1 is intended to be the ungrounded baseline.

## Step 2: Create a Focused Space

1. Go to [GitHub Copilot Spaces](https://github.com/copilot/spaces).
2. Select **Create space**.
3. Name it `Copilot Demo - Calculator Review`.
4. Choose your personal account as the owner, then select **Create Space**.
5. Add these instructions to the Space:

   ```text
   Review this Python Streamlit calculator using only the sources in this Space.
   Cite the exact file and function for each code finding. Apply the repository
   coding rules when making recommendations. Separate observed behavior from
   recommendations, and do not generate code unless asked.
   ```

6. Select **Add sources** and add these individual files from `ajafry/copilot-demo-dev-01`:

   - `views/calculator.py`
   - `.github/copilot-instructions.md`

Individual files are intentional here: GitHub loads their full contents into the Space context for each question. These sources stay synchronized with their latest versions on the repository's `main` branch. Uncommitted local changes are not included.

## Step 3: Ask the Same Question in the Space

1. Open the chat in `Copilot Demo - Calculator Review`.
2. If the model picker is available, select the same model used in Step 1.
3. Submit the exact same prompt without adding more detail:

   ```text
   You are planning a small improvement to the calculator in this repository.
   What are the two most important code-quality risks in its expression
   evaluation, and which repository coding rules should guide the fix?
   Cite the files and functions that support your answer. Do not write code.
   ```

## Step 4: Compare the Responses

The response from the Space should be more grounded. Look for these differences:

| Without a Space | With the Space |
| --- | --- |
| May not know which repository or calculator is intended | Knows the two supplied repository files |
| May provide generic advice about calculator validation | Can inspect `_evaluate` in `views/calculator.py` |
| Cannot reliably identify this repository's conventions | Can apply `.github/copilot-instructions.md` |
| May invent filenames or implementation details | Should cite actual files and functions from the sources |

The exact wording can vary by model. Judge the answers by evidence and relevance, not by length.

Discussion:

- Which statements in the second answer came directly from the Space sources?
- Did the Space reduce assumptions or follow-up questions?
- What other recurring task would benefit from a small, curated Space?

## Cleanup

Delete the Space if it was created only for this demonstration. No repository files or GitHub issues are changed by this exercise.

## Current References

- [Asking GitHub Copilot questions in GitHub](https://docs.github.com/en/copilot/how-tos/chat-with-copilot/chat-in-github)
- [About GitHub Copilot Spaces](https://docs.github.com/en/copilot/concepts/context/spaces)
- [Creating GitHub Copilot Spaces](https://docs.github.com/en/copilot/how-tos/provide-context/use-copilot-spaces/create-copilot-spaces)
- [Using GitHub Copilot Spaces](https://docs.github.com/en/copilot/how-tos/provide-context/use-copilot-spaces/use-copilot-spaces)