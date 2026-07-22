# Prosecutor Artifact

This repository contains the artifact accompanying our paper titled **Prosecutor: Bayesian Counterfactual Fault Localization**.

The artifact includes:

* The complete implementation of **Prosecutor**.
* Implementations of the **8 baseline fault localization techniques** used in our evaluation.
* Scripts for reproducing the experimental results presented in the paper.
* Supporting data and infrastructure required to execute the experiments.

---

# Artifact Structure

The artifact is organized as follows:

* **`prosecutor/`** – implementation of Prosecutor.
* **Baseline implementations** – implementations of the eight competing fault localization techniques used in our evaluation.
* **Experimental scripts** – scripts for reproducing the experimental results.
* **Supporting data** – benchmark programs, execution traces, and intermediate artifacts required by the experiments.

The artifact can be built and executed in two ways:

1. **Docker (Recommended)** – provides a fully configured environment with all required dependencies.
2. **Manual installation** – installs all dependencies directly on the host machine.

> **Important**
>
> When performing a manual installation, you must update the base directory used throughout the project. Specifically, modify the base path defined in the source files and helper scripts so that it matches the directory where the artifact is installed. Failure to do so will result in incorrect file paths during execution.

---

# System Requirements

The artifact has been tested on **x86-64 Linux**.

The following software is required:

* Java
* Python 3
* Git
* Defects4J
* Docker (optional, recommended)

---

# Running with Docker (Recommended)

Build the Docker image:

```bash
docker build -t prosecutor .
```

Run the container:

```bash
docker run -it prosecutor
```

The Docker image contains all required dependencies and is ready to execute the experiments.

---

# Manual Installation

## 1. Create a Python Virtual Environment

From the project root (`/app`):

```bash
python3 -m venv .env
source .env/bin/activate
pip install -r requirements.txt
```

---

## 2. Verify Defects4J

Verify that Defects4J has been installed correctly:

```bash
defects4j info -p Lang
```

The command should complete successfully without reporting any errors.

---

## 3. Build Bingo

Prosecutor relies on Bingo which calls LibDAI for marginal inference.

```bash
cd /app/FaultLocalization/prosecutor/bingo
./scripts/build.sh
```

---

## 4. Build Prosecutor

```bash
cd /app/FaultLocalization
source build.sh
```

---

## 5. Start the Dependency Daemons

Before running Prosecutor, launch the dependency analysis services:

```bash
java -cp build/classes:libs/* IntraproDependencies >/dev/null 2>&1 &
java -cp build/classes:libs/* InterproDependencies >/dev/null 2>&1 &
```

---

# Running Prosecutor

Execute

```bash
python3 get-result.py
```

The script runs Prosecutor on the configured Defects4J bugs and produces output similar to the following:

```text
==================== Results for Lang 1 when CFX = True ====================
Rank = 2 among 20 lines for /app/FaultLocalization/prosecutor/simplebug/trace_Lang_1_buggy
Statement = org.apache.commons.lang3.math.NumberUtils.createNumber:471 with Confidence = 0.691035

Rank = 2 among 20 lines for /app/FaultLocalization/prosecutor/simplebug/trace_Lang_1_buggy
Statement = org.apache.commons.lang3.math.NumberUtils.createNumber:471 with Confidence = 0.765946

MinRank = 2 among 20 lines for /app/FaultLocalization/prosecutor/simplebug/trace_Lang_1_buggy
Statement = org.apache.commons.lang3.math.NumberUtils.createNumber:471
```

## Understanding the Output

Prosecutor reports three rankings.

### **Rank (F)**

The first ranking is computed by constructing the Execution Provenance Graph (EPG) using **only failing executions**.

The reported confidence equals

> **1 − suspiciousness score**

where a larger confidence indicates that Prosecutor has greater confidence that the statement is faulty.

### **Rank (F + P)**

The second ranking is obtained by constructing the EPG using **both failing and passing executions**.

### **MinRank**

The final ranking reported by Prosecutor.

For each program element,

```
MinRank = min(Rank(F), Rank(F + P))
```

This corresponds to the full Prosecutor system described in the paper.

---

# Selecting Benchmark Bugs

The benchmark projects and bug IDs to evaluate are configured in

```text
get-result.py
```

Modify the project names and bug identifiers in this script to evaluate different Defects4J versions.

---

# Running the Baselines

The script

```text
GenerateActiveBugs.py
```

can execute either Prosecutor or any of the baseline techniques evaluated in the paper.

Select the desired approach by uncommenting one of the following lines:

```python
# Mutation-Based Fault Localization (MBFL)
# test_mutation_based_approaches(args.proj_name, args.bug_id, optimized=True)

# Spectrum-Based Fault Localization (SBFL)
# test_spectrum_based_approaches(args.proj_name, args.bug_id)

# Prosecutor
test_prosecutor(args.proj_name, args.bug_id, cfx=True)
```

Only one method should be enabled at a time.

---

# Reproducing the Experimental Results

The provided scripts reproduce the results reported in the paper.

To evaluate additional bugs, simply modify the project name and bug ID in `get-result.py` (or the corresponding experiment script) and rerun the experiment.

Depending on the benchmark and selected technique, experiments may require a significant amount of time to complete.

---

# Third-Party Components

This artifact incorporates third-party software used during our evaluation.

### Bingo

We use the implementation of **Bingo** developed by Raghothaman *et al.* in:

> **User-Guided Program Reasoning using Bayesian Inference**

### Fault Localization Data

We also adapted portions of the scripts from the Defects4J fault localization framework:

https://bitbucket.org/rjust/fault-localization-data

These scripts were partially reused to implement the mutation-based fault localization baselines.

---

# Citation

If you use this artifact in your research, please cite our paper.

```bibtex
% Citation information will be added after publication.
```

---

# Contact

For questions regarding the artifact or reproduction of the experiments, please contact the authors through the contact information provided in the paper.
