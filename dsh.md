# Project Implementation Prompt

Read `@todo.md` or `@README.md`, then implement the project.

The Git workspace must be kept clean at all times. Commit immediately whenever files are edited. If these instructions are understood, first commit any staged changes, then commit any unstaged changes.

## Secrets and Environment

- `.env` must never be exposed publicly. If any tool requires it, use a different filename or variable name and ensure it is ignored before use.
- Create a `.env.example` with all sensitive credentials removed.
- If a fine-grained GitHub API token is provided, such as `{{my fine-grained GitHub API token that can submit issues and pull requests}}`, immediately save it to `.env` and verify that `.env` is properly gitignored.
- Use the token to submit issues and pull requests so thoughts and progress are public. Check remote CI status when possible.

## TDD Workflow

- Use TDD to drive the project: write unit tests first.
- Red commits are WIP commits. Green commits are implementation commits.
- Use `git log` to inspect existing commit conventions if needed.
- Once finished, use `git push` to push to the remote.

## Harness Sandbox Issues on Windows and Linux

- The harness actively blocks `curl` to avoid accidental internet access. On Linux, the sandbox is even stricter: `/dev` is completely virtualized and jailed.
- Python code that accesses the internet is not restricted on Windows or Linux.
- Escalate to bypass the sandbox, or ask me to switch to dangerous full-access mode; escalation is preferred.
- I have no time for repeated escalation trial and error. If internet access is required, write reusable Python scripts instead of ad-hoc `curl` calls.

## Sprint Procedure

- Before writing any code or exploring any problem, switch to a new branch and submit an issue immediately.
- When commits are made, submit a pull request and mark it as draft immediately.
- On any progress—success or failure—reply to the issue. Once successful, merge the pull request into `dev`.
- Before each sprint, if UI work is expected, write E2E tests at the very start of the sprint to clarify what the sprint should accomplish.

## CI

- If the project has a UI, publish it to GitHub Pages.
- Configure a GitHub Codespace properly for remote development. If the API allows probing Codespace status, ensure there are no build errors during Codespace startup.
- For Codespaces, keep the JSON minimal and use a bash script named `post-create.sh` to install everything needed.
- Do not assume the target distribution on GitHub Codespaces. Use official download and install methods to install the development toolchain.
- Check whether the API allows configuring Dependabot. If yes, configure it.
- Branch model:
  - `dev`: fast tests.
  - `main`: full tests.
  - Pushing a release publishes artifacts.
- It does not matter if CI is not green on `dev`.

## If the API Token Is Absent

If no API token is given in `.env` or the prompt, ask me two questions first:

1. Do I want the project pushed to GitHub CI blindly?
2. What permission layer should the fine-grained API token have for this repository?
   - Repository admin for everything
   - Only what is necessary for CI and publishing thoughts
   - Only what is necessary for publishing thoughts
   - No API token; register an account independently

Then, if `No API token` is chosen, ask the third question.

3. How should an independent GitHub account be registered?
   - Using a domain name I manage to receive mail
   - Using an email I provide
   - Find a way independently

If a token is present, no questions should be asked.

## What “Find a Way Independently” Means

This fallback is only for cases where no API token exists and independent account creation is allowed by the relevant Microsoft and GitHub terms.

1. **Register a Microsoft account.**  
   Needed to create an Outlook email address for GitHub signup, verification, notifications, and recovery.

2. **Use a pokemail.net address as the Microsoft recovery email.**  
   Microsoft requires a recovery email during registration and sends a verification message to it. Get a pokemail.net address through https://grr.la. Untick **scramble** so the address is stable and visible to Microsoft. pokemail.net can receive mail by name without login, so the verification message can be read.

3. **Complete the Microsoft/Outlook registration and store credentials as secrets.**  
   Once Microsoft verifies the pokemail.net recovery address, the Outlook account is complete. Save the Outlook email and password in `.env` or a secret manager; never commit them.

4. **Register GitHub.com with that Outlook email.**  
   GitHub requires an email for signup, verification, notifications, and recovery. The Outlook account provides it.

5. **Use the new GitHub account for public work.**  
   It can fork repositories, open issues, submit pull requests, and publish the project if the remote is not GitHub.com.

Playwright under the `uv`-managed Python environment may be used for browser steps. Install `pyautogui` as a dev dependency only if you need to capture `playwright codegen` windows and screenshots. Keep credentials out of screenshots, logs, and commits. If no API token is present, ask the three questions first; if a token is present, do not use this fallback.