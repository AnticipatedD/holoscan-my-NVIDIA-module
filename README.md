# holoscan-my-NVIDIA-module 

![holoscan-my-NVIDIA-module_skills](holoscan-my-NVIDIA-module_skills.jpg)

NVIDIA Holoscan 5.0 is now generally available, our framework for building and deploying real-time, streaming AI pipelines with robotic control in one unified runtime. This major release includes a rebuilt core execution framework, zero-copy IPC messaging, installable domain modules, and agentic AI tooling.

> This page is for version 5.0 (Latest) (default).
> For other versions, use one of these documentation indexes:
> - 5.0 (Latest) (default): [Latest Release](https://docs.nvidia.com/holoscan/sdk-user-guide/5-0-latest/llms.txt)
> - ltsb-2.0: [User-Guide](https://docs.nvidia.com/holoscan/sdk-user-guide/ltsb-2-0/llms.txt)
> - 4.6: [User-Guide](https://docs.nvidia.com/holoscan/sdk-user-guide/4-6/llms.txt)
> - 4.5: [User-Guide](https://docs.nvidia.com/holoscan/sdk-user-guide/4-5/llms.txt)
> - 4.4: [User-Guide](https://docs.nvidia.com/holoscan/sdk-user-guide/4-4/llms.txt)
> - 4.3: [User-Guide](https://docs.nvidia.com/holoscan/sdk-user-guide/4-3/llms.txt)
> - Archives: [User-Guide-Archives](https://docs.nvidia.com/holoscan/sdk-user-guide/archives/llms.txt)

> For clean Markdown of any page, append .md to the page URL.
> For a complete documentation index, see [NVIDIA-Docs](https://docs.nvidia.com/holoscan/sdk-user-guide/llms.txt).
> For AI client integration (Claude Code, Cursor, etc.), connect to the MCP server at [NVIDIA-MCP](https://docs.nvidia.com/holoscan/sdk-user-guide/_mcp/server).

Welcome to holoscan-my-NVD-module SDK 5.0. This Getting Started track takes you from an empty project to a running multi-rate sensor-fusion pipeline: a 60 Hz camera and a high-rate IMU feeding a fusion operator that drives a control output. Each chapter adds exactly one capability, and every chapter's code builds on the previous one, so by the end you have written — not just read about — a complete application that composes, compiles, and runs.

Application graphs can be authored in C++ or Python. This track's operator
implementation and deep reference chapters are C++ topics; Python applications compose registered C++ operators through discoverable proxies and can define their own operators, as the [Python guide](/holoscan/sdk-user-guide/python) describes. The lifecycle and graph chapters show both spellings where the supported surfaces are equivalent.

> **We will learn**
>
> * How the compose → compile → run model applies in both C++ and Python
> * Which chapters define C++ operators and which also apply to Python applications
> * Where to find runnable examples and language-specific guidance

<title id="gs-fusion-topology-title">
  Running example: camera and IMU sources fused into a control output, driven by one clock
</title>

<defs>
  <path d="M0,0 L10,5 L0,10 z" fill="currentColor" />

  <path d="M0,0 L10,5 L0,10 z" fill="#76B900" />
</defs>

<rect x="60" y="60" width="150" height="60" rx="8" fill="currentColor" opacity="0.06" />

Camera
source · 60 Hz

<rect x="60" y="170" width="150" height="60" rx="8" fill="currentColor" opacity="0.06" />

IMU
source · high-rate

<rect x="320" y="115" width="150" height="60" rx="8" fill="currentColor" opacity="0.06" />

Fusion
multi-rate contract

<rect x="570" y="115" width="140" height="60" rx="8" fill="#76B900" opacity="0.25" />

`Control`
`output`
`tutorial path`
`clock drives the graph`

The full track introduces checked errors and time before the sensor-fusion example. C++ `compute()` returns `holoscan::expected<T, Error>`; composition mistakes may throw and compilation returns an inspectable plan. Operator timing
comes from temporal contracts, including the supported input-driven default when `contract()` is omitted.

## Prerequisites

#### C++

* **Linux** (x86_64 or aarch64).
* A **C++20 toolchain** and CMake 4.0 or newer.
* A **CUDA-capable NVIDIA GPU** — optional until the final chapter
(Tensors 101); everything before it runs on the CPU.
* The SDK installed — follow
[SDK installation](/holoscan/sdk-user-guide/setup/sdk-installation) first if we have not.

#### Python

* **Linux** (x86_64 or aarch64) and Python 3.12 or newer.
* An SDK build or installation that includes the `holoscan` Python package.
See [Python installation](/holoscan/sdk-user-guide/python/installation).
* A **CUDA-capable NVIDIA GPU** only when the application uses GPU-backed
operators or tensors; the minimal graph-authoring example runs on the CPU.

## The chapter sequence

Work through the chapters in order; each assumes the ones before it.

1. [Core concepts](/holoscan/sdk-user-guide/getting-started/core-concepts) —
the three-phase model (compose → compile → run) and the vocabulary the
whole guide uses.
2. [Errors as values](/holoscan/sdk-user-guide/getting-started/errors-as-values) — `holoscan::expected<T, Error>`, branching on `ErrorCode`, and the `HOLOSCAN_TRY` macro that keeps error-handling readable.
3. [Time and clocks](/holoscan/sdk-user-guide/getting-started/time-and-clocks) — how the runtime models time, and why every sample carries a timestamp.
4. [Our first operator](/holoscan/sdk-user-guide/getting-started/first-operator) — an operator as three declarations: `setup()` for structure, `contract()` for time, `compute()` for work.
5. [Typed parameters](/holoscan/sdk-user-guide/getting-started/typed-parameters) — declaring configuration with schemas so bad config fails before run.
6. [Composing a graph](/holoscan/sdk-user-guide/getting-started/composing-a-graph) — wiring operators together through typed ports with `add_flow()`.
7. [Compile and check](/holoscan/sdk-user-guide/getting-started/compile-and-check) — `compile()` turns our composed graph into an execution plan, or returns a rejected plan that tells you exactly why.
8. [Running and stopping](/holoscan/sdk-user-guide/getting-started/running-and-stopping) —starting the plan, observing it, and shutting down cleanly.
9. [Our first pipeline](/holoscan/sdk-user-guide/getting-started/first-pipeline) — assembling the camera + IMU fusion pipeline end to end, with its multi-rate temporal contract.
10. [Tensors 101](/holoscan/sdk-user-guide/getting-started/tensors-101) —GPU payloads: borrowing tensors from preallocated pools with `allocate_tensor()`, and why we never synchronize on the hot path.

> **Note**
>
> **Why errors and time come first.** In Holoscan 5.0, failure handling and
> temporal behavior are not advanced topics — they are the calling convention.
> APIs return `expected` values you must branch on, and `compute()` never
> reasons about time because `contract()` already did. Chapters 2 and 3 give
> we those two lenses; every chapter after them assumes we have both.

## Where to go afterward

When the track is complete, the fusion pipeline we built continues as the running example in [Building operators](/holoscan/sdk-user-guide/building-operators/operator-anatomy) and the full worked applications in [Holoscan by example](/holoscan/sdk-user-guide/examples/holoscan-by-example),
including the `camera_imu_control` and `slow_fast_fusion` example apps that ship with the SDK.

## Start with the minimal application

The shipped [`first_pipeline` C++ example](https://github.com/nvidia-holoscan/holoscan-sdk/tree/v5.0.0/examples/first_pipeline/cpp)
demonstrates the complete lifecycle. Build and run it from its `cpp/` directory:

```bash
cmake -B build -S .
cmake --build build
./build/first_pipeline
```

For the corresponding Python authoring shape, start with the [Python overview](/holoscan/sdk-user-guide/python). Its snippets use an explicitly installed Operator provider; this source tree does not yet ship a standalone Python sibling for `first_pipeline`.

## Recap

* C++ and Python applications use the same compose → compile → run lifecycle.
* C++ and Python both define operators; Python also composes registered C++ operators through discoverable proxies.
* Start with the language tabs on the graph, compile, and run chapters, then use the Python section for installation and language-specific behavior.
