# Label-Hub Selenium Automation

Selenium-based UI automation scripts for validating core workflows in the **Label Hub** platform, including authentication, user management, project management, file upload, annotation/validation flows, and admin operations.

---

## Table of Contents
- [Overview](#overview)
- [Repository Structure](#repository-structure)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Test Environment Assumptions](#test-environment-assumptions)
- [How to Run](#how-to-run)
- [Test Coverage](#test-coverage)
- [Known Limitations](#known-limitations)
- [Troubleshooting](#troubleshooting)
- [Recommendations for Improvement](#recommendations-for-improvement)

---

## Overview

This repository contains Python Selenium scripts intended to automate end-to-end testing of Label Hub web workflows hosted at:

- `http://182.163.99.86/login`

The automation targets realistic role-based scenarios for:

- **Admin** operations (login, users, projects, configuration/data actions)
- **Annotator** workflow (project search, annotation actions, logout)
- **Validator** workflow (validation actions with and without edits)

The suite includes both:

1. **Reusable procedural helpers** (in `final1.py`) for common actions.
2. **`unittest`-based automated test class** (in `final2.py`) focused on annotator login/action/logout.

---

## Repository Structure

```text
.
├── README.md
├── final1.py   # Procedural Selenium action library + flow functions
└── final2.py   # unittest-based annotator scenario tests
```

### `final1.py`
Contains helper functions for major system actions, including:

- Session setup with Selenium WebDriver (Edge)
- Login/Logout
- User navigation and user creation/deletion
- Project creation/deletion
- File upload flow
- Project search
- Annotation and validation actions

### `final2.py`
Contains a `unittest.TestCase` (`annotator`) that automates:

- Annotator login
- Project search and annotation interaction
- Logout verification

---

## Prerequisites

Before running scripts, ensure you have:

- **Python 3.9+** (recommended)
- **Google Chrome** (for `final2.py`) and matching **ChromeDriver** available in PATH
- **Microsoft Edge** (for `final1.py`) and matching **Edge WebDriver** available in PATH
- Access to the target Label Hub server (`http://182.163.99.86`)
- Test user credentials with proper role permissions

Python packages required:

- `selenium`
- `pynput`

---

## Installation

1. Clone the repository:

```bash
git clone <your-repo-url>
cd Label-Hub-Selenium-Automation
```

2. (Recommended) Create and activate a virtual environment:

```bash
python -m venv .venv
source .venv/bin/activate   # Linux/macOS
# .venv\Scripts\activate    # Windows PowerShell
```

3. Install dependencies:

```bash
pip install selenium pynput
```

4. Verify browser drivers:

- `chromedriver --version`
- `msedgedriver --version`

Driver versions should match installed browser versions.

---

## Test Environment Assumptions

The current scripts assume:

- The application is available at fixed URLs under `http://182.163.99.86`.
- Certain UI elements are located via absolute XPath selectors.
- Some test data exists already (e.g., searchable project/group names).
- A local upload file path may be required and valid for file-upload test flow.

---

## How to Run

### Run `unittest` scenario (annotator flow)

```bash
python final2.py
```

### Use reusable function flow (procedural)

`final1.py` exposes functions that can be called from a custom runner script, for example:

- `Login(...)`
- `UserCreate(...)`
- `projectCreate(...)`
- `File_Upload(...)`
- `Annotation(...)`
- `Validation(...)`
- `Logout()`

If you use `final1.py` directly, create your own orchestration script that imports and calls these functions in sequence according to your scenario.

---

## Test Coverage

The repository is designed around 30 functional test intents across core platform workflows.

### 1) Authentication
- Invalid login attempts (wrong ID / missing password / missing both)
- Incomplete ID scenarios
- Successful login
- Logout verification

### 2) User Management
- Create user with missing required fields (role, email, name, etc.)
- Password rule boundary testing
- Large input values (name/password)
- Invalid email and phone formats
- Duplicate or conflicting user checks
- User deletion

### 3) Project Management
- Create project
- Prevent duplicate project creation
- Delete project

### 4) File Handling
- Upload files to project
- Validate upload interaction path

### 5) Role-Based Workflow
- Annotator actions
- Validator actions (with and without edits)
- Rejected annotator review path

### 6) Admin Operations
- Configuration actions
- Data view operations

---

## Known Limitations

Current scripts are functional but include maintainability and robustness risks:

1. **Heavy absolute XPath usage**
   - UI layout changes can break tests quickly.

2. **Hardcoded URLs and sample data**
   - Environment switching requires manual edits.

3. **Hardcoded local upload path**
   - File upload relies on machine-specific location.

4. **Sleep-based synchronization**
   - `time.sleep(...)` usage can cause flakiness under variable network/app latency.

5. **Mixed browser usage**
   - `final1.py` uses Edge, while `final2.py` uses Chrome.

6. **Duplicate method name in `final2.py`**
   - `test_logout` appears twice; one definition overrides the other.

---

## Troubleshooting

### WebDriver launch failures
- Confirm browser is installed.
- Confirm matching driver version is installed and in PATH.

### Element not found / timeout errors
- The UI might have changed; update locators.
- Increase `WebDriverWait` timeouts if environment is slow.
- Prefer stable locators (`id`, `name`, `data-testid`) over long XPath chains.

### Upload automation issues
- Verify the file path exists on your current machine.
- Confirm OS-level file picker interaction is supported in your execution environment.

### Intermittent flaky results
- Replace unnecessary `sleep` calls with explicit waits.
- Add retry logic only where business-safe and deterministic.

---

## Recommendations for Improvement

To evolve this into a production-grade automation suite:

1. **Adopt Page Object Model (POM)** to centralize locators and page actions.
2. **Move configuration to environment variables** (base URL, credentials, data files).
3. **Use `pytest` + reporting plugins** (HTML/JUnit) for better CI observability.
4. **Replace absolute XPath selectors** with resilient locators.
5. **Split tests by feature and role**, and add setup/teardown isolation.
6. **Add CI pipeline integration** (GitHub Actions/GitLab CI/Jenkins).
7. **Introduce test data factories/fixtures** for repeatable, independent test runs.
8. **Add linting and formatting** (`ruff`, `black`) for maintainability.

---
