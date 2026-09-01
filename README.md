# GWFlow

**Resource-Aware Scheduling of Gravitational-Wave Analysis Workflows**

GWFlow is a small scientific-computing project investigating whether parallel and resource-aware execution can reduce the runtime of gravitational-wave data-analysis workflows.

## Motivation

Future gravitational-wave observatories such as the Einstein Telescope are expected to process very large data volumes and large numbers of candidate signals. Scientific workflows may combine several computationally different analysis methods, making efficient scheduling and parallel execution increasingly important.

This project uses a gravitational-wave analysis workflow combining conventional matched filtering and machine-learning-based anomaly detection as an example heterogeneous scientific workload.

## Installation

Install the required Python packages with:

```bash
pip install -r requirements.txt

## Workflow

The analysis consists of:

1. Selecting real complex IQ noise data.
2. Generating and injecting simulated gravitational-wave chirps.
3. Analysing each injected signal using:

   * matched filtering;
   * an autoencoder-based anomaly detector.
4. Executing the two independent analysis branches sequentially and in parallel.
5. Using Ray to schedule parallel analysis tasks.
6. Comparing runtime and throughput as the number of analysed signals increases.

Conceptually:

```text
             Injected GW signal
                    |
           -------------------
           |                 |
    Matched filtering    ML inference
           |                 |
           ------- Ray -------
                    |
                 Results
```

## Methods

Each analysis input consists of a 1024-sample complex IQ segment.

The matched-filter and machine-learning analyses use the same relevant frequency range of 0.8–1.86 MHz.

The matched-filter branch performs complex correlation with a template generated using the same chirp and resonance model as the injected signal.

The machine-learning branch uses a trained autoencoder. Signal candidates are identified using the reconstruction-loss detection threshold.

Ray is used to schedule independent matched-filter and machine-learning analysis tasks concurrently.

## Preliminary Performance

Initial benchmarks demonstrate the advantage of parallel execution once Ray workers are initialized.

| Number of signals | Sequential runtime | Ray runtime | Speed-up |
| ----------------: | -----------------: | ----------: | -------: |
|                50 |            1.24 s |     0.55 s |    2.25× |
|               100 |            2.49 s |     1.1 s |    2.27× |

The initial small-workload execution also illustrates the fixed worker-initialization overhead associated with distributed execution. For larger workloads, this overhead becomes less important and parallel execution provides approximately a two-fold runtime improvement in the current local configuration.

## Current Scope

This implementation runs on a local machine and serves as a proof of concept for scientific workflow orchestration. The same architecture can be extended to heterogeneous CPU/GPU resources, larger workloads, distributed computing environments, and intelligent resource-allocation strategies.

## Technologies

* Python
* NumPy
* TensorFlow
* Ray
* Matplotlib
* Scientific signal processing
* Gravitational-wave data analysis
