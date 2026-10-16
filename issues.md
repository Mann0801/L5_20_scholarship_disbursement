# Issues Found and Fixed

Line numbers refer to the original `scholarship_disbursement.py`.

| Issue | Type | Line(s) | Description | Fix Approach |
|-------|------|---------|-------------|--------------|
| SQL injection (Bandit B608, Medium) | Security | 10 | INSERT query built with `%` string formatting, so a student/scheme name containing `'` could alter or break the SQL | Use `?` placeholders and pass values as a parameter tuple |
| Mutable default argument (Pylint W0102) | Bug | 9 | `audit=[]` is created once and shared across every call to `grant`, so the audit list keeps growing between unrelated calls | Default to `None` and create a new list inside the function |
| Shell command execution (Bandit B605, B607) | Security | 26 | `os.system("echo ...")` starts a shell with a partial executable path; unsafe if the command ever includes input | Replace with `print()`; removed unused `import os` |
| Try/except/pass + broad exception (Bandit B110, Pylint W0718) | Bug / Security | 25-28 | `except Exception: pass` silently swallows every error, hiding failures | Removed the try/except since `print` needs no error handling |
| Global state (Pylint W0603, C0103) | Code quality | 6, 16 | `global disbursed_total` mutated inside `disburse`; module-level variable could drift from the database | Removed the global; added `disbursed_total()` that sums disbursed amounts from the DB |
| Inconsistent return (Pylint R1710) | Bug | 15-21 | `disburse` returns an amount on success but falls off the end (implicit `None`) otherwise | Return `None` explicitly for an unknown award id |
| Comparison to None (Pylint C0121, Flake8 E711) | Style / Bug | 32 | `scheme == None` can be overridden by `__eq__`; identity check is correct | Use `scheme is None` |
| Non-snake_case function name (Pylint C0103) | Style | 31 | `bySchemeCount` violates PEP 8 naming | Renamed to `by_scheme_count` |
| Missing docstrings (Pylint C0114, C0116) | Documentation | 1, 9, 15, 24, 31, 37 | Module and functions have no docstrings | Added module and function docstrings |
| Lines too long (Flake8 E501, Pylint C0301) | Style | 5, 10, 17, 19, 34 | Lines exceed 79 characters (up to 130) | Split SQL strings and calls across multiple lines |
| Unguarded `main()` call | Code quality | 45 | Importing the module runs the demo | Wrapped in `if __name__ == "__main__":` |

## Results

| Tool | Before | After |
|------|--------|-------|
| Pylint | 5.00/10 (16 messages) | 10.00/10 |
| Bandit | 4 issues (1 Medium, 3 Low) | No issues identified |
| Flake8 | 6 issues | 0 issues |

The program output is unchanged (`50000 None`, `1 50000`, `archiving awards`).
