dockerfile path
test path
environment/
# Harbor Assignment — Log Time-Series Metrics Task

This repository contains a Harbor-compatible benchmark task that evaluates an AI agent's ability to parse web server access logs and compute time-series metrics.

The task is designed so that:

- **Oracle agent score:** 1.0
- **NOP agent score:** 0.0

This ensures proper validation of agent correctness.

---

## Problem Description

You are given a web server access log file located at:

`/app/access.log`

The log follows the standard Apache Combined Log Format.

Your objective is to parse this log file and generate a metrics file at:

`/app/metrics.json`

---

## Required Metrics

For each **minute bucket**, compute the following:

### Fields

- **timestamp**
  - Format: `YYYY-MM-DD HH:MM`
  - Example: `"2024-10-10 13:05"`
- **total_requests**
  - Total number of requests in that minute
- **error_count**
  - Number of requests with HTTP status codes in the range:
    - 400–499
    - 500–599
- **error_rate**
  - Calculated as:
    
    `error_rate = error_count / total_requests`
  - Must be rounded to exactly **2 decimal places**

---

## Output Format

The output must be a valid JSON array written to:

`/app/metrics.json`

**Example:**

```json
[
  {
    "timestamp": "2024-10-10 13:00",
    "total_requests": 15,
    "error_count": 11,
    "error_rate": 0.73
  },
  {
    "timestamp": "2024-10-10 13:01",
    "total_requests": 8,
    "error_count": 2,
    "error_rate": 0.25
  }
]
```

### Important Requirements

The output must satisfy **all** of the following:

- Must be valid JSON
- Must be a JSON array
- Must be sorted in ascending order by timestamp
- Must include all minute buckets present in the log
- Must use absolute paths (`/app/...`)
- Must round `error_rate` to exactly 2 decimal places

---

## Repository Structure

```
.
├── task.toml
├── instruction.md
├── README.md
│
├── environment/
│   ├── Dockerfile
│   └── generate_logs.py
│
├── solution/
│   └── solve.sh
│
└── tests/
    ├── test.sh
    └── test_outputs.py
```

---

## Component Explanation

### task.toml
Defines Harbor task metadata, including:
- instruction path
- dockerfile path
- test path
- resource limits

### environment/
Contains files used to build the execution environment.

#### Dockerfile
- Builds container image
- Installs dependencies
- Generates input log at `/app/access.log`

#### generate_logs.py
- Generates deterministic access logs
- Uses fixed random seed
- Ensures reproducible testing

### solution/
Contains the reference solution used by the Oracle agent.

#### solve.sh
- Parses `/app/access.log`
- Computes required metrics
- Writes output to `/app/metrics.json`

### tests/
Contains validation logic.

#### test.sh
- Executes tests
- Runs pytest validation

#### test_outputs.py
- Validates:
  - Output file exists
  - JSON format correctness
  - Correct number of entries
  - Proper sorting
  - Correct metric calculations

---

## How Harbor Evaluates This Task

Two agents are used:

### Oracle Agent
- Runs the reference solution
- Produces correct output
- **Expected reward:** 1.0

### NOP Agent
- Does nothing
- Produces no output
- **Expected reward:** 0.0

---

## Harbor Validation Commands

Run from Harbor repository root:

### Install dependencies
```sh
uv sync
```

### Run Oracle agent
```sh
uv run harbor run --agent oracle --path harbor_tasks/log-time-series --job-name oracle-test
```
**Expected Result:**
Reward: 1.0

### Run NOP agent
```sh
uv run harbor run --agent nop --path harbor_tasks/log-time-series --job-name nop-test
```
**Expected Result:**
0.0

---

## Design Decisions

- Deterministic log generation for consistent validation
- Absolute file paths for Harbor compatibility
- Strict JSON validation
- Explicit timestamp sorting
- Precise rounding of `error_rate`
- Minimal dependencies for fast execution

---

## Author

Aishwarya

---

Harbor Assignment Submission  
SDE Intern Evaluation Task



