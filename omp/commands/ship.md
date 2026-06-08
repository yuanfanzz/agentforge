# Ship Command

Complete development with verification gate and structured finish.

## Steps

### 1. Verification Gate

Invoke `skill://verification-before-completion`:
- IDENTIFY the commands that prove completion
- RUN each command fresh
- READ the full output
- Only proceed if all evidence confirms completion

### 2. Finish Branch

Invoke `skill://finishing-a-development-branch`:
- Structured merge/PR/discard workflow
- Test verification before any final action
- Present options: merge directly, create PR, or discard
