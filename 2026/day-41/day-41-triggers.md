# Day 41 – Triggers & Matrix Builds

## Task 1: Trigger on Pull Request

### Create .github/workflows/pr-check.yml and configure it to run only when a pull request is opened or updated against the main branch. Add a step that prints PR check running for branch: <branch name>. Then create a new branch, push a commit, and open a pull request against main. Finally, verify that the workflow runs automatically and appears on the pull request page.

```yaml

name: PR Check

on:
  pull_request:
    branches:
      - main
    types:
      - opened
      - synchronize

jobs:
  pr-check:
    runs-on: ubuntu-latest

    steps:
      - name: PR Check
        run: 'echo "PR check running for branch: ${{ github.head_ref }}"'

```


<img width="1365" height="732" alt="image" src="https://github.com/user-attachments/assets/8d48a6ca-e9b1-4d09-a15e-3f8e0378abdf" />

<img width="1365" height="682" alt="image" src="https://github.com/user-attachments/assets/47aaf834-fccf-4ae9-a7ef-a671f10c1a1b" />


Verify: Does it show up on the PR page?

<img width="1365" height="731" alt="image" src="https://github.com/user-attachments/assets/a30e6445-7d1f-4841-9475-4a7b5f549de1" />


## Task 2: Scheduled Trigger

### Add a schedule: trigger to any workflow using cron syntax and configure it to run every day at midnight UTC.

```yaml
name: Scheduled Automation
on:
  schedule:
    - cron: '30 5 * * *'  

jobs:
  run-tasks:
    runs-on: ubuntu-latest
    steps:
      - name: Execute the Script
        run: echo "Workflow triggered successfully!"

```

### Write in your notes: What is the cron expression for every Monday at 9 AM?

cron : '0 9 * * 1'


## Task 3: Manual Trigger

### Create .github/workflows/manual.yml with a workflow_dispatch: trigger, add an input for the environment name with staging or production as options, print the selected input value in a step, and go to the Actions tab to find the workflow and click Run workflow.

```yaml

name: Get the details of env

on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Select the target environment'
        required: true
        default: 'staging'
        type: choice
        options:
          - staging
          - production

jobs:
  show-environment:
    runs-on: ubuntu-latest

    steps:
      - name: Display selected environment
        run: echo "Selected environment is ${{ inputs.environment }}"
```

<img width="1365" height="727" alt="image" src="https://github.com/user-attachments/assets/e8d84098-9771-4e77-b788-283e16f96070" />

### Verify: Can you trigger it manually and see your input printed?

<img width="1364" height="728" alt="image" src="https://github.com/user-attachments/assets/d67ab750-7a74-4558-aecf-8cffda1c8731" />


## Task 4: Matrix Builds

### Create .github/workflows/matrix.yml using a matrix strategy to run the same job for Python 3.10, 3.11, and 3.12, install each Python version, print its version, and verify that all three jobs run in parallel.

```yaml

name: To install Python and check its version

on:
  push:

jobs:
  test:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        python-version: ["3.10", "3.11", "3.12"]

    steps:
      - uses: actions/checkout@v4

      - name: Set up Python ${{ matrix.python-version }}
        uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}

      - name: Check Python version
        run: python --version

```

<img width="1365" height="728" alt="image" src="https://github.com/user-attachments/assets/09b01724-9d39-45da-8891-0ad346fe2df0" />

### Then extend the matrix to also include 2 operating systems — how many total jobs run now?

```yaml

name: To install Python and check its version

on:
  push:

jobs:
  test:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        os: [windows-latest, macos-latest]
        python-version: ["3.10", "3.11", "3.12"]

    steps:
      - uses: actions/checkout@v4

      - name: Set up Python ${{ matrix.python-version }}
        uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}

      - name: Check Python version
        run: python --version
```

6 jobs will run in parallel 

<img width="1365" height="730" alt="image" src="https://github.com/user-attachments/assets/990892b2-1653-49eb-af79-805bb03c049b" />


## Task 5: Exclude & Fail-Fast

### In your matrix, exclude one specific combination (e.g., Python 3.10 on Windows)

```yaml

name: To install Python and check its version

on:
  push:

jobs:
  test:
    runs-on: ${{ matrix.os }}

    strategy:
      matrix:
        os: [windows-latest, macos-latest]
        python-version: ["3.10", "3.11", "3.12"]
        exclude:
          - os: windows-latest
            python-version: "3.10"

    steps:
      - uses: actions/checkout@v4

      - name: Set up Python ${{ matrix.python-version }}
        uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}

      - name: Check Python version
        run: python --version
```


<img width="1363" height="726" alt="image" src="https://github.com/user-attachments/assets/9876f7b2-988b-4813-900a-e7c96904fb08" />

### Set fail-fast: false — trigger a failure in one job and observe what happens to the rest

task, add fail-fast: false under strategy and intentionally make one matrix job fail.

```yaml
name: Python Matrix Test

on:
  push:

jobs:
  test:
    runs-on: ${{ matrix.os }}

    strategy:
      fail-fast: false
      matrix:
        os: [windows-latest, macos-latest]
        python-version: ["3.10", "3.11", "3.12"]

        exclude:
          - os: windows-latest
            python-version: "3.10"

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python ${{ matrix.python-version }}
        uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}

      - name: Check Python version
        run: python --version

      - name: Intentionally fail one job
        if: matrix.os == 'windows-latest' && matrix.python-version == '3.11'
        run: exit 1
```

<img width="1365" height="689" alt="image" src="https://github.com/user-attachments/assets/7307faec-531a-48cf-948d-4e7400eeb7b7" />

### Write in your notes: What does fail-fast: true (the default) do vs false?

fail-fast: true (default): If one matrix job fails, GitHub Actions cancels the remaining in-progress and queued matrix jobs.

fail-fast: false: If one matrix job fails, the other matrix jobs continue running and complete independently.
