# Scholarship Disbursement Ledger

A small Python ledger for a scholarship office. It grants awards under different schemes, disburses funds, archives records and counts awards per scheme, using an in-memory SQLite database.

The code was cleaned up using static analysis with **Pylint**, **Bandit** and **Flake8**.

## Run

```bash
python scholarship_disbursement.py
```

## Static analysis results

| Tool | Before | After |
|------|--------|-------|
| Pylint | 5.00/10 | 10.00/10 |
| Bandit | 4 issues (1 Medium, 3 Low) | No issues |
| Flake8 | 6 issues | 0 issues |

Main fixes:
- SQL injection: string-formatted query replaced with parameterized `?` placeholders
- Mutable default argument `audit=[]` replaced with `None`
- `os.system` shell call and silent `except Exception: pass` removed
- Global state removed; disbursed total is computed from the database

## Files

- `scholarship_disbursement.py`: the cleaned source
- `issues.md`: table of issues found and how each was fixed
- `reflection.md`: reflection on using static analysis
- `*_report_before.txt` / `*_report_after.txt`: tool reports before and after the fixes
