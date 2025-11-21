# Git Workflow and Guidelines

This document outlines version control practices, commit message standards, and pull request guidelines.

## Core Git Principles

### Repository Management

- If the project isn't in a git repo, STOP and ask permission to initialize one
- YOU MUST STOP and ask how to handle uncommitted changes or untracked files when starting work
- Suggest committing existing work first
- When starting work without a clear branch for the current task, YOU MUST create a WIP branch
- Track ALL non-trivial changes in git
- Commit frequently throughout the development process, even if high-level tasks are not yet done

### Critical Git Rules

**FORBIDDEN GIT FLAGS:**
- ❌ `--no-verify`
- ❌ `--no-hooks`
- ❌ `--no-pre-commit-hook`

**Before using ANY git flag:**
1. State the flag you want to use
2. Explain why you need it
3. Confirm it's not on the forbidden list
4. Get explicit user permission for any bypass flags

### Git Best Practices

- NEVER use `git add -A` unless you've just done a `git status`
- Don't add random test files to the repo
- Check git status before commits
- Keep changes minimal and focused

## Pre-commit Hook Protocol

### Mandatory Pre-Commit Failure Protocol

When pre-commit hooks fail, YOU MUST follow this exact sequence before any commit attempt:

1. **Read** the complete error output aloud (explain what you're seeing)
2. **Identify** which tool failed (ruff, prettier, tests, etc.) and why
3. **Explain** the fix you will apply and why it addresses the root cause
4. **Apply** the fix and re-run hooks
5. **Verify** all hooks pass before committing

**NEVER commit with failing hooks. NEVER use `--no-verify`. If you cannot fix the hooks, you MUST ask for help rather than bypass them.**

### Pressure Response Protocol

When asked to "commit" or "push" and hooks are failing:

- Do NOT rush to bypass quality checks
- Explain: "The pre-commit hooks are failing, I need to fix those first"
- Work through the failure systematically
- Remember: Quality over speed, even when waiting

**User pressure is NEVER justification for bypassing quality checks.**

### Accountability Checkpoint

Before executing any git command, ask yourself:

- "Am I bypassing a safety mechanism?"
- "Would this action violate the instructions?"
- "Am I choosing convenience over quality?"

If any answer is "yes" or "maybe", explain your concern before proceeding.

### Learning-Focused Error Response

When encountering tool failures (ruff, pytest, etc.):

- Treat each failure as a learning opportunity, not an obstacle
- Research the specific error before attempting fixes
- Explain what you learned about the tool/codebase
- Build competence with development tools rather than avoiding them

**Quality tools are guardrails that help you, not barriers that block you.**

## Commit Messages

### Format and Style

Write a concise, informative commit message that:

- Has a short summary on the first line
- Reviews the whole context to understand the effect of changes
- Explains the **why** behind changes, not just the what
- Mentions the files that were changed and what was changed
- Uses bullet points for multiple changes
- Helps someone familiar with the codebase understand the intent

### Commit Message Structure

```
Summary of changes

- First change and its effect
- Second change and its effect
- Additional changes as needed
```

### Guidelines

- Do NOT guess about intent - be specific about the effect
- Be specific about how changes affect the rest of the code
- The goal: someone familiar with the codebase but not with these changes should understand why they were made
- Think carefully about what would be most helpful for understanding the commit intent
- If there are no changes or input is blank, return a blank string

### Commit Trailers

**For bug fixes or features based on user reports:**
```bash
git commit --trailer "Reported-by:<name>"
```

**For commits related to a GitHub issue:**
```bash
git commit --trailer "Github-Issue:#<number>"
```

### FORBIDDEN in Commits

- ❌ NEVER mention `co-authored-by` or similar aspects
- ❌ NEVER mention the tool used to create the commit message or PR
- ❌ No temporal references like "new", "improved", "updated recently"

## Pull Requests

### PR Title Guidelines

Write a concise, informative pull request title:

- Very short summary in imperative mood
- Explain the **why** behind changes more than the changes themselves
- Keep the title under 50 characters
- Use action verbs (Add, Fix, Update, Remove, Refactor)

**Examples:**
- Good: "Fix auth timeout in API client"
- Bad: "Updated the authentication code"

### PR Body/Message Guidelines

Write a clear, informative pull request message in markdown:

#### Structure

1. **Summary** - Brief overview of the changes
2. **Details** - Explain the why behind changes
3. **Changes** - Bulleted list outlining all changes
4. **Files Changed** - Mention specific files and what changed
5. **Closed Issues** - List any issues this resolves
6. **Haiku** - End with a haiku summarizing the changes

#### Example PR Body

```markdown
## Summary

This PR fixes the authentication timeout issue that was causing API requests to fail after 30 seconds.

## Why These Changes

The default timeout was too short for some API endpoints, particularly during high-load periods. This change increases the timeout and adds retry logic.

## Changes

- Updated `api_client.py` to increase default timeout from 30s to 60s
- Added exponential backoff retry logic in `retry_handler.py`
- Modified `config.py` to allow timeout configuration via environment variable
- Added tests for timeout and retry behavior in `test_api_client.py`

## Files Changed

- `src/api_client.py`: Increased timeout, added retry configuration
- `src/retry_handler.py`: Implemented exponential backoff
- `src/config.py`: Added TIMEOUT_SECONDS environment variable
- `tests/test_api_client.py`: Added timeout and retry tests

## Closes

- Closes #123
- Closes #456

---

*API calls wait*
*Patience rewarded with time*
*Retry finds the way*
```

### PR Best Practices

- Create detailed messages about what changed
- Focus on the high-level description of the problem and solution
- Don't go into code specifics unless it adds clarity
- Think carefully before writing your PR title and body
- If there are no changes or input is blank, return a blank string

### FORBIDDEN in Pull Requests

- ❌ NEVER mention `co-authored-by` or similar aspects
- ❌ NEVER mention the tool used to create the commit message or PR
- ❌ No claims that something is "working" when functionality is disabled or broken

## Branch Management

### Creating Branches

- Create feature branches for new work
- Use descriptive branch names
- Common patterns:
  - `feature/description`
  - `fix/issue-description`
  - `refactor/component-name`

### Working on Branches

- Prefer working on the main branch unless specified
- Worktrees and feature branches are alternatives
- Default is to work on main (unless project requires otherwise)
- When creating a WIP branch, use clear naming

## Common Git Workflows

### Starting New Work

```bash
# Check status
git status

# Create new branch if needed
git checkout -b feature/my-feature

# Or create WIP branch
git checkout -b wip/task-description
```

### Committing Changes

```bash
# Check what's changed
git status
git diff

# Stage specific files (avoid git add -A)
git add file1.py file2.py

# Commit with message
git commit -m "Summary of changes

- Detail about change 1
- Detail about change 2"

# Or with trailer
git commit --trailer "Github-Issue:#123"
```

### Before Pushing

```bash
# Ensure all tests pass
pytest

# Ensure formatting is correct
ruff format .
ruff check .

# Ensure types are correct
pyright

# Check status one more time
git status

# Push to remote
git push origin feature/my-feature
```

## Troubleshooting

### Uncommitted Changes

If you have uncommitted changes when starting new work:

1. STOP and check `git status`
2. Ask how to handle the changes
3. Options:
   - Commit them first
   - Stash them
   - Create a WIP commit
4. Wait for explicit instruction

### Pre-commit Hook Failures

See "Pre-commit Hook Protocol" section above. Never bypass hooks.

### Merge Conflicts

1. Identify conflicting files with `git status`
2. Review conflicts carefully
3. Resolve conflicts while preserving intent of both changes
4. Test thoroughly after resolving
5. Mark as resolved and commit

## Quality Checklist

Before committing and pushing:

- ✅ Ran `git status` to see what's changed
- ✅ All tests passing
- ✅ All formatters run (ruff, prettier, etc.)
- ✅ All type checks passing
- ✅ Pre-commit hooks passing
- ✅ Commit message is clear and explains why
- ✅ No forbidden flags used
- ✅ Changes are minimal and focused
- ✅ No random test files added

Before creating a PR:

- ✅ Title is under 50 characters and explains why
- ✅ Body has summary, details, and changes list
- ✅ Files changed are documented
- ✅ Related issues are referenced
- ✅ Haiku included (if using the template)
- ✅ No mention of tools or co-authors
- ✅ All commits have clear messages
