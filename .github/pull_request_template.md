## What this PR does

<!-- One paragraph summary of the change -->

## Which files changed and why

<!-- List the key files modified and the reason for each -->

- `file.md` — reason for change

## Type of change

- [ ] Bug fix (non-breaking change that fixes an issue)
- [ ] New agent or pipeline stage
- [ ] Enhancement to existing agent
- [ ] Quality gate change
- [ ] Documentation update
- [ ] Breaking change (existing pipelines may need adjustment)

## Testing

<!-- How did you verify this works? -->

- [ ] Ran a full pipeline end-to-end with the change
- [ ] Tested the specific agent in isolation with `/run-agent`
- [ ] Verified quality gates pass/fail correctly
- [ ] Checked that existing pipeline behaviour is not broken

## Checklist

- [ ] Agent files follow the existing format (Inputs, Output, What You Do, Rules sections)
- [ ] Any new agent has a completion signal documented in `references/agent-handoffs.md`
- [ ] Quality gates updated if pipeline stages changed
- [ ] README.md file structure section updated if files were added/removed
- [ ] No API credentials or secrets included in the change
- [ ] Commit messages are clear and descriptive
