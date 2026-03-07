# Fake TA pipeline

A simple example of a centralized test automation pipeline using GitHub Actions and pytest.

It shows how tests can be:
- executed directly inside the TA repository
- triggered from another repository by using a reusable workflow

A small set of dummy tests is included for demonstration purposes.

## Structure

```
├── .github/workflows/
│   ├── internal-pytest-job.yml
│   └── external-pytest-job.yml
├── tests/
│   ├── smoke/
│   ├── integration/
│   └── e2e/
├── pytest.ini
├── README.md
└── requirements.txt
```

Tests are organized by folders (smoke, integration, e2e) so that external/other repositories
can run each subset sequentially.  
Core idea is to run the test packs in order, based on their expected execution speed (fastest first, for 
most immediate feedback) and to block the slowest tests from executing when a failure is already present.  

## Internal pipeline

The internal workflow (`internal-pytest-job.yml`) would be run when developing the test suite itself.

Steps:
- Execute a fake pre-commit step
- If pre-commit succeeds, execute all test suites in parallel using a matrix

```yaml
matrix:
  test-type: [smoke, integration, e2e]
```

Each job runs:
```bash
pytest tests/<test-type>
```

## Reusable workflow for other repositories

The repository also exposes a reusable GitHub Actions workflow (`external-pytest-job.yml`) that 
other repositories can call.

The workflow:
- Checks out this repository
- Installs dependencies (or fetches them from cache)
- Runs each requested test suite

You can observe an example of how to implement another repository triggering the tests here: 
https://github.com/ukk0/fake-service-pipeline/tree/main

This example demonstrates how a central test automation repository can provide reusable 
pipelines for other services.