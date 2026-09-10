# agentci_pr_tests

A throwaway fixture for exercising [AgentCI](https://github.com/youssefmohamed0/hackathon)'s
`github/` module end to end against real GitHub.

**`main` is deliberately broken and should stay that way.** `calc.py` subtracts where it
should add, and `test_calc.py` catches it. That failing test is the whole point: it gives
the agent something real to fix, and its exit code is the oracle that decides whether the
fix worked.

## Running against it

```bash
GITHUB_PR_ENABLED=true GITHUB_TOKEN=<a token with push access> \
  python -m agentci run https://github.com/AbdelrahmanAmr2205/agentci_pr_tests \
    --test-cmd "pytest -q" --pr
```

A green run pushes `agentci/fix-<run_id>` and opens a pull request against `main`. Don't
merge them — merging one fixes the bug and the fixture stops being useful. Close them,
or leave them; the branch name carries the run id either way.

## What a correct result looks like

- Exactly **one** commit on the branch, parented on `main`'s tip — no `agentci baseline`
  commit, which is the thing most likely to regress
- **Only `calc.py`** in the diff. `test_calc.py` is a protected path; if it ever shows up
  in a pull request, the tamper check has a hole in it
- The PR body carries the model's own root-cause summary plus the test command, the
  verdict, the attempt count and the run id
