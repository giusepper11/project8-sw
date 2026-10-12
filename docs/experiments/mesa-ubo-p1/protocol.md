# Free Skate protocol — preparation only

Status: **BLOCKED, NOT EXECUTED**. No deployment or hardware execution is
authorized. Switch 1 / GM20B; HOS and exact scene remain unset. The five native
game constant bindings are dynamic and bypass P1; do not execute this protocol
as a P1 performance experiment until a relevant ordinary fragment binding is
identified and observed. Hardware testing requires explicit approval.

Use the [frozen shared measurement contract](https://github.com/giusepper11/switch-research-knowledge/blob/b5507b6d40f29c53dd33954fc5755c5721552c0a/proposals/mesa-switch-measurement-contract.md).
Its historical P1 section describes an earlier proposal/control. For this task,
both variants use Mesa `fe0282715a329ce2c5702b5893e65cc5e54d7f67` and only
the option differs. Baseline 606441 is provenance, not the primary control.

## Pending single-binary configuration

After all build gates clear, configure the existing Switch build with:

```sh
cmake -S src/game -B build/mesa-p1 -G Ninja -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_TOOLCHAIN_FILE="$PWD/tools/switch/switch-rexglue-gcc.cmake" \
  -DREXSDK_DIR=/absolute/patched-rexglue \
  -DREXGLUE_SWITCH_NVK_SDK=/absolute/authenticated-p1-sdk
cmake --build build/mesa-p1
```

These commands are a pending recipe, not evidence of a build. Use the same
authenticated SDK, generated code, toolchain/image digest, compiler flags,
shader translator and supporting prerequisites. Do not apply game Mesa patches
0001–0004. Do not use an existing build directory or overwrite an SDK archive.
Record full source/patched-tree and input/output SHA-256 identities locally.

Start with an exact private copy of `release/thps_p8.toml` for each process.
Add precisely one of these lines (no duplicate `switch_mesa_env` keys):

```toml
# Control
switch_mesa_env = "NVK_SWITCH_CPU_RESOLVE_UBO=0;NVK_SWITCH_PERF_LOG=1"
```

```toml
# Candidate
switch_mesa_env = "NVK_SWITCH_CPU_RESOLVE_UBO=1;NVK_SWITCH_PERF_LOG=1"
```

The only config byte difference must be P1's 0/1. Timing instrumentation is
identical. Validate the TOML diff and capture actual environment/startup logs;
setting/logging an option is not proof of route selection. Do not inherit
unrecorded NVK/Mesa options or enable `NVK_DEBUG=push_dump` (known split-span
diagnostic concern in the retained P1 review). Keep `switch_ab_lap_after_s=0`:
that profiler's A/B lap intentionally makes the image wrong and cannot be a
correctness/performance control. Disable its manual trigger during runs.
Use independently recorded processes; never hot-switch recorded commands.

Freeze release settings including native renderer, vblank minimum 2,
pace-before-present true, MSAA false, anisotropy 2, depth24 true, record thread
true, async pipelines as actually resolved, profiling and all guest waits.
These are controlled inputs, not endorsements of the release's hardware claims.
Keep GPU profiling off until calibrated; if enabled later, enable it equally
in a separate GPU experiment. Preserve resolution/display refresh explicitly.

## Workload identity and caches

Before approval record the exact Free Skate menu selection/location, supported
game executable hash, save/checkpoint hash (local only), skater, position,
camera, route/input sequence and ambient/AI conditions. No exact repeatable
scene is established from the available source/docs; this is a prerequisite,
not a slot to fill with an invented location. Prefer a stationary fixed camera
and no-input state or an existing reproducible replay. If AI/streaming cannot
be controlled, record those differences and restrict the resulting claim.

Use identical fresh-process launch sequence. Proposed starting windows: warm
up 120 s after entering the frozen scene, then collect 120 s; extend equally
if pipeline/texture activity has not stabilized. Freeze these durations before
data collection. Exclude launch/menu/loading from the scene window but retain
their logs. Count drawn/skipped draws, submissions, pipelines, textures and
eligible ordinary fragment bindings per window; similar FPS or duration does
not prove matched work. If eligibility is zero, classify this as a no-op
comparison, never a P1 speedup.

Port driver cache lives under `cache_root/shaders/driver/native-*.cache` and
may load a larger `.tmp` file. Preserve both originals. Use fresh exclusive
experiment directories; never delete or overwrite the user's caches. The SDK
disables generic Mesa shader disk cache, but the port explicitly persists
VkPipelineCache. Freeze a compatible P1 warm cache snapshot, authenticate it
and restore an identical private copy before each warm pass; retain mutations
after each process. Analyze cold-empty cache passes separately. Do not reuse
release cache compiled with 0002 merely because its filename matches. Cache
compatibility must pass actual link/compiler/runtime checks first.

## Environment and artifact record

For each run retain ID, option, same NRO/ELF/archive SHA-256, ELF build ID,
Mesa source/clean state/build manifest, SDK source plus patch/submodule hashes,
consumer commit, toolchain versions/image digest and exact compile/link output.
Keep game/codegen identities local and never publish their bytes.
Record model/SoC, HOS/CFW, loader/launch route, power/performance mode,
docked/handheld state, observed CPU/GPU/EMC clocks, temperature, battery/charger,
resolution/refresh and SD/cache state. Setting a clock is not observing it.
Stock and overclocked profiles are different experiments. No historical HOS or
clock profile is assumed to describe this game.

## Required metrics and observability gates

| Layer | Metric / source | Limitation to resolve before accepting results |
| --- | --- | --- |
| Eligibility/selection | count fixed fragment UBO_DESC bindings, accepted CPU resolutions and fallback reasons | No game route counter exists. Dynamic UBO direct=true would not identify P1. Any proposed observer must discriminate cbuf type/stage/set and attribute scene draws; no probe was added. |
| CPU recording | per-window recording elapsed ns, per drawn draw and eligible bind; port renderer draw/state timings | Existing 10 s native logs are aggregates; record-thread timing is not isolated driver command recording. Do not label total thread time as driver time. |
| CPU submission | Horizon exec/submit/kickoff calls and ns; separate lock/throttle/fence wait ns | PERF_LOG enables timing at device creation. Distinguish nested counters; do not add overlapping times. |
| Submission pressure | submitted_entries/dwords, entries per draw/submission, inflight peaks, waits, resource_retries, submit_failures | Existing get_stats APIs/teardown summaries are not a per-frame game integration; retain before/after window counters and in-session failure evidence if a minimal reviewed adapter later becomes feasible. Historical saturation is not presumed. |
| GPU | calibrated pass/frame elapsed time | Recalibrate against independent wall time/known intervals at actual clocks, check units/period/wrap and uncertainty. Never import the historical 1.6/1.627 multiplier. |
| Player-visible | presented frame-time p50/p95/p99, >33.3/>50 ms fractions, cadence/stutters | Existing slow-frame logs do not provide complete presented-frame samples. CPU loop time is not presentation time. |
| Correctness/faults | matched-scene images plus visual transitions, errors/device loss, complete exit/fault logs | Similar FPS and no teardown warning are insufficient; missing evidence prevents acceptance. |

Sources: [native logs](https://github.com/R-drg/project8-sw/blob/3393f2615deb7e8ac11ce51d2cd0e9defded5669/src/game/src/native/thps_native_system.cpp#L587),
[Horizon counters](https://github.com/giusepper11/mesa-switch/blob/fe0282715a329ce2c5702b5893e65cc5e54d7f67/src/nouveau/horizon/nouveau_horizon.h#L280),
[timing selection](https://github.com/giusepper11/mesa-switch/blob/fe0282715a329ce2c5702b5893e65cc5e54d7f67/src/nouveau/vulkan/nvkmd/switch/nvkmd_switch_dev.c#L1262).
Collect counter deltas at equal workload boundaries, not process-lifetime totals
including different loading. Route logging is correctness-only unless overhead
is separately measured and controlled. No instrumentation that changes rendering
is acceptable. A missing required metric remains an explicit measurement gate.

## Future comparison and acceptance

Correctness gate first: menu, 3D scene, depth/stencil, loading/transitions,
cache reuse, exit/resume and sustained operation; compare matched output and
fault behavior. Any new rendering mismatch or device fault stops P1 evaluation.

Preregister primary CPU recording ns per matched drawn draw and GPFIFO entries
per matched drawn draw, conditional on nonzero qualifying P1 bindings. Treat
submission ns and player-visible frame distributions as separate outcomes;
GPU time cannot substitute for CPU cost. Preserve primary definitions and
denominators after seeing data.

Start with at least three valid off and three on passes, randomized/reversed
order across fresh processes. Include repeated off/off placebo passes and
instrumentation off/on calibration when feasible, with identical cache and
clock conditions. More repetitions are required if noise remains high.
Retain failures/incomplete runs with reasons and raw data; never drop bad runs
silently or merge cache-cold and warm populations.

Report distributions/paired differences and uncertainty. A gain requires the
measured primary difference to exceed observed placebo/control noise, survive
reversed order, and preserve correctness/fault behavior and frame pacing.
Otherwise report INCONCLUSIVE. A Project 8 result alone cannot establish a
shared multi-port gain. No measurements or improvement claims accompany this
preparation.
