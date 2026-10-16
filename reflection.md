# Reflection

## 1. Which issues were the easiest to fix, and which were the hardest? Why?

The easiest were the style issues: renaming `bySchemeCount` to `by_scheme_count`, changing `== None` to `is None`, adding docstrings and wrapping long lines. Each one is a mechanical change that cannot alter behaviour.

The hardest was the `global disbursed_total` warning. Removing the `global` meant deciding where the running total should live. I computed it from the database (`SUM(amount) WHERE disbursed = 1`), which keeps one source of truth, but I had to check that the program still printed the same output. The SQL injection fix was simple to write but needed care to get the placeholder syntax right.

## 2. Did the static analysis tools report any false positives? If so, describe one example.

Bandit's B605 on `os.system("echo archiving awards")` is close to a false positive: the command is a fixed string with no user input, and Bandit itself says it "seems safe". It was still worth fixing, because a shell was unnecessary for printing a message, and the code would become dangerous if someone later added a variable to the command. Pylint's missing-docstring messages are not bugs either, but they are useful for maintainability rather than false.

## 3. How would you integrate static analysis tools into your actual software development workflow?

- Run Flake8 and Pylint locally through a pre-commit hook, so style problems are caught before a commit is made.
- Add a CI step (for example, GitHub Actions) that runs Pylint, Flake8 and Bandit on every push and pull request. The build fails if Bandit finds Medium or High issues or if the Pylint score drops below a threshold such as 9.5.
- Keep shared configuration (`.pylintrc`, `setup.cfg`/`.flake8`) in the repo so everyone uses the same rules, and use `# nosec` or disable comments only with a justification.

## 4. What tangible improvements did you observe in the code quality, readability, or potential robustness after applying the fixes?

- **Security:** the SQL injection hole is closed, and there is no shell call left.
- **Correctness:** the shared mutable default and the silent `except: pass` are gone, and `disburse` has a clear, explicit return contract.
- **Robustness:** the disbursed total comes from the database, so it cannot get out of sync with the records.
- **Readability:** consistent naming, docstrings on every function, and short lines make the code easier to read. Importing the module no longer runs the demo.
- **Scores:** Pylint went from 5.00 to 10.00/10, Bandit from 4 issues to 0, and Flake8 from 6 issues to 0, with the program output unchanged.
