# Lab 5 — Reflection

> The repository README refers to four reflection questions, but the attached handout does not include a separate list of questions. The following responses address four prompts based on the handout's stated learning outcomes; they can be adjusted to match any questions supplied separately by the instructor.

## 1. What is static code analysis, and how does it help improve software quality?

Static code analysis checks source code without running it. In this lab, Pylint highlighted maintainability and correctness concerns, Flake8 identified formatting violations, and Bandit surfaced security risks. Using them together helped uncover problems that a successful demo run would not reveal, such as a shell-injection risk and shared mutable state.

## 2. What issues did the tools and program review uncover, and how were they fixed?

Bandit identified shell execution using `shell=True`, binding to all network interfaces, and an unused hard-coded password-like value. I removed the unused shell runner and credential and changed the hub's default host to loopback. Pylint identified a mutable default argument, inconsistent/missing return behavior, non-idiomatic names, missing docstrings, and an implicit text encoding. I replaced the shared default with per-device settings, made device operations handle missing devices safely, used `snake_case` and docstrings, and specified UTF-8. I also inspected the demo output and caught the shared-settings bug: the thermostat's temperature was appearing in the light's settings. Flake8's spacing and line-length issues were corrected.

## 3. Which tool was most useful, and why?

Bandit was especially useful for prioritizing risk because it identified the high-severity shell-execution pattern and the unsafe network binding. Pylint was valuable for broad code-quality feedback, while Flake8 provided a quick, objective checklist for style. The tools complemented one another rather than duplicating the same checks.

## 4. What are the limitations of static analysis, and how would you use it in future work?

Static analysis can flag risky patterns but cannot prove that the complete program is correct or secure. It may not understand runtime context, and it cannot replace tests, code review, or careful inspection of behavior. In future work I would run these checks automatically during development or in CI, review the findings by severity, and add tests for important behavior. For this lab, I also used focused runtime assertions because static analysis alone would not have confirmed that each device received an independent settings dictionary or that thermostat values were clamped correctly.
