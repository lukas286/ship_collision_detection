# Ship Collision Detection

Exercise for the course **Data Integration and Large-Scale Analysis (DIA)**, WiSe 2026/27, TU Berlin (DAMS Lab, Prof. Matthias Boehm).

The goal is to identify near-collision scenarios between vessels in AIS (Automatic Identification System) data. The analysis is implemented both locally with **pandas** (single- and multi-threaded) and distributed with **PySpark**, including optimizations for runtime and scalability.

## Data

Historical AIS data is provided by the [Danish Maritime Authority](http://aisdata.ais.dk/). A full day contains about 15 million AIS reports (~2.5 GB), so a pre-filtered dataset from **2025-01-01** with the most relevant vessels is used.

Of the 26 features, only these are relevant:

| Feature | Description |
|---|---|
| `MMSI` | Maritime Mobile Service Identity, unique vessel identifier |
| `# Timestamp` | Date and time of the AIS report |
| `Longitude`, `Latitude` | Position of the vessel |
| `SOG` | Speed Over Ground, velocity relative to the Earth's surface |
| `Heading` | Compass direction the vessel points in |

## Setup

Requires **Python 3.12**.

```powershell
py -3.12 -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

Alternatively, dependencies can be installed with Poetry via `pyproject.toml`.

## Tasks

### 1. Implementation with pandas (`collisionDetectionPandas.py`)

- **1.1 Single-threaded processing:** Implement `processSingleThreaded()`. Using the provided `getData()` and `checkCollision()`, return a data frame with the MMSI and timestamp of both records for each vessel pair on a collision course.
- **1.2 Partitioning:** Implement `partitionData()` to split the data frame into multiple partitions for data-parallel execution, while ensuring correctness and completeness. The constants `DISTANCE_THRESHOLD` and `LOOKAHEAD_TIME` are hard constraints that can be used to define partitions.
- **1.3 Multi-threaded processing:** Implement `processMultiThreaded()`, which processes the partitions from `partitionData()` in parallel and returns a single result data frame in the same format as 1.1.

### 2. Implementation with PySpark (`collisionDetectionSpark.py`)

- **2.1 Pre-processing:** Implement `preprocess()` analogous to the pandas version, with input and output of type `pyspark.pandas.DataFrame`.
- **2.2 Partitioning:** Implement `partition()`. Evaluate whether the pandas partitioning strategy can be reused or whether Spark-specific strategies improve runtime further.
- **2.3 Processing:** Implement `process()` using a PySpark join. It receives the partitions from `partition()` and returns the MMSI and timestamp of both colliding vessels as a `pyspark.pandas.DataFrame`.

### 3. Comparison

- **3.1 Runtime:** Measure and compare the runtime of pandas single-threaded, pandas multi-threaded, and PySpark. State expectations beforehand and check whether the measurements match.
- **3.2 Scalability:** Evaluate how the three approaches scale, where clear limitations show up, and what to expect when scaling up the deployment further.

## Constraints

- Do not install additional dependencies.
- Keep the function signatures as they are.
- Only modify the framework code in the designated sections.
- Code for runtime measurement and scalability evaluation is not part of the final submission.