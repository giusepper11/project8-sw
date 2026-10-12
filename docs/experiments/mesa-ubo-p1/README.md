# Mesa UBO P1 consumer preparation

Decision: **BLOCKED** as the requested real-game validation consumer.
Captured 2026-10-11. Platform: Nintendo Switch 1 / GM20B. Target HOS and
game-state identity: not frozen; firmware applicability unknown. This is a
public-source investigation and host preparation, not a hardware result.

The native renderer's five game constant buffers use
`VK_DESCRIPTOR_TYPE_UNIFORM_BUFFER_DYNAMIC`. Mesa lowers those to
`NVK_CBUF_TYPE_DYNAMIC_UBO`, whose address is already resolved from root
descriptor state before the P1 branch. P1 only changes eligible ordinary
`NVK_CBUF_TYPE_UBO_DESC` fragment bindings. A toggle comparison on these game
constants would test an unchanged route, not P1. This is SUPPORTED for the
inspected source flow (PINNED_SOURCE and SOURCE_INFERENCE), not a claim that
every SDK/UI/helper draw in a future executable has zero eligible bindings.

Evidence: [renderer layouts and writes](https://github.com/R-drg/project8-sw/blob/3393f2615deb7e8ac11ce51d2cd0e9defded5669/src/game/src/native/native_renderer.cpp#L1387),
[per-draw dynamic offsets](https://github.com/R-drg/project8-sw/blob/3393f2615deb7e8ac11ce51d2cd0e9defded5669/src/game/src/native/native_renderer.cpp#L3639),
[binding](https://github.com/R-drg/project8-sw/blob/3393f2615deb7e8ac11ce51d2cd0e9defded5669/src/game/src/native/native_renderer.cpp#L824),
[lowering](https://github.com/giusepper11/mesa-switch/blob/fe0282715a329ce2c5702b5893e65cc5e54d7f67/src/nouveau/vulkan/nvk_nir_lower_descriptors.c#L290),
[resolver](https://github.com/giusepper11/mesa-switch/blob/fe0282715a329ce2c5702b5893e65cc5e54d7f67/src/nouveau/vulkan/nvk_cmd_buffer.c#L1761),
and [guard](https://github.com/giusepper11/mesa-switch/blob/fe0282715a329ce2c5702b5893e65cc5e54d7f67/src/nouveau/vulkan/nvk_descriptor_set_ubo.h).
A qualifying runtime record of an ordinary fixed fragment binding attributable
to Free Skate would refine this conclusion. No such record was collected.

Full build is independently BLOCKED: no legitimate local game inputs or
generated translation were found in the inspected project/game/download
locations, no authenticated Mesa SDK archives were found, and the Switch
toolchain is unavailable. No generated outputs were invented. Renderer,
gameplay, shaders, Mesa, frame pacing and release settings are unchanged.

## Frozen sources and preservation

| Repository / role | Full commit |
| --- | --- |
| [R-drg/project8-sw](https://github.com/R-drg/project8-sw/tree/3393f2615deb7e8ac11ce51d2cd0e9defded5669), public game source / upstream HEAD | `3393f2615deb7e8ac11ce51d2cd0e9defded5669` |
| [danfromtico/mesa-switch](https://github.com/danfromtico/mesa-switch/tree/d4a00ea0ab3f59afb967cc5d779e4263d237bd77), original patch base, Mesa 26.2.3 | `d4a00ea0ab3f59afb967cc5d779e4263d237bd77` |
| [giusepper11/mesa-switch](https://github.com/giusepper11/mesa-switch/tree/606441c90e459028a50fca352daebfc20211f890), shared baseline / public HEAD | `606441c90e459028a50fca352daebfc20211f890` |
| [giusepper11/mesa-switch](https://github.com/giusepper11/mesa-switch/tree/fe0282715a329ce2c5702b5893e65cc5e54d7f67), P1 / draft PR 1 head, Mesa 26.2.4 | `fe0282715a329ce2c5702b5893e65cc5e54d7f67` |
| [rexglue/rexglue-sdk](https://github.com/rexglue/rexglue-sdk/tree/f5337cdc947ff6d4c4196737e2c807a48f2a1fc2), v0.10.0 | `f5337cdc947ff6d4c4196737e2c807a48f2a1fc2` |
| [switch-research-knowledge](https://github.com/giusepper11/switch-research-knowledge/tree/983d01e6fdc19e108c03dfa5529da8864df9e7f1), research checkout / draft PR 22 head | `983d01e6fdc19e108c03dfa5529da8864df9e7f1` |
| [measurement contract proposal](https://github.com/giusepper11/switch-research-knowledge/blob/b5507b6d40f29c53dd33954fc5755c5721552c0a/proposals/mesa-switch-measurement-contract.md) | `b5507b6d40f29c53dd33954fc5755c5721552c0a` |

Fresh public clone/ref/API checks confirmed these heads and draft PRs remain
open. P1's sole parent is the shared baseline; its diff touches only the guard,
resolver integration, host test and Meson test registration. The original
26.2.3 base is an ancestor of the shared baseline, but the intervening 122-file
change is not equivalent to the game's patch stack. In particular, Horizon
GPFIFO reservation/capacity accounting and dedicated stencil-plane memory
handling changed before P1. Historical saturation must be remeasured.

The research checkout was clean and retained its branch and all evidence.
The adjacent switch-framegen-native checkout had untracked user work; it was
only inventoried and left untouched. Public sources were cloned into new
`/tmp/project8-p1-*` directories. All 16 game SDK patches were applied in listed
order only to the fresh SDK clone, with exit 0 and a passing diff check; no
SDK submodules or toolchain build was performed. Mesa remains clean at P1.
No reset, clean, stash, rebase, force push or original-upstream modification
was used. An existence check returned 404 before creating the personal fork
`giusepper11/project8-sw`; its parent was confirmed as `R-drg/project8-sw`.

No separate P1 integration was found in the inspected game tree: its equivalent
is historical patch 0001 and its general environment hook. Remote branch/PR
coverage outside the fetched refs is not an exhaustive ecosystem search.
The patched SDK does contain ordinary UBOs in the emulated Xenos Vulkan
command processor; this does not establish their execution in the native
Free Skate workload. Switching to Xenos would change the renderer/workload and
is outside this mission. Native-helper attribution remains unverified.

## Patch compatibility matrix

All comparisons below are PINNED_SOURCE, SUPPORTED within the exact commits
above. Applicability is corroborating host evidence, not semantic equivalence.
Patch files have no standalone license header. The game repository carries
GPL-3.0 and its NOTICE; affected NVK/NAK files carry MIT notices, while os_misc
has its own permissive notice. Do not infer a standalone MIT grant for every
new patch line from the destination file alone. No patch code is copied or
imported by this preparation.

| Patch / origin and targets | P1 behavior comparison | Dependencies, risks and disposition |
| --- | --- | --- |
| [0001](https://github.com/R-drg/project8-sw/blob/3393f2615deb7e8ac11ce51d2cd0e9defded5669/patches/mesa-switch/0001-nvk-switch-resolve-set-ubo-descriptors.patch), Project 8; `nvk_cmd_buffer.c`, `nvk_cmd_buffer_get_cbuf_addr` | **CONFLICTING / partially present**: both attempt CPU reads of ordinary set UBO descriptors. Original checks map, update-after-bind layout flag and an addition-based bound; P1 additionally requires fragment, SM53/non-bindless, fixed UBO binding/type/stride/count, whole-set binding flags, safe subtraction bounds and address/range representation. Original is unconditional on Switch and lacks the option. Dynamic UBO handling precedes both. | Do not apply original over P1. `git apply --check` exits 1. P1 replaces the idea with narrower reviewed behavior, not identical semantics. Early-consumption/lifetime and rejected-set coverage remain important. Keep P1 unmodified. |
| [0002](https://github.com/R-drg/project8-sw/blob/3393f2615deb7e8ac11ce51d2cd0e9defded5669/patches/mesa-switch/0002-nak-gm20b-latency-and-flattening.patch), Project 8 attributing nfsmw-nx/MIT; `nak/opt_instr_sched_common.rs:estimate_variable_latency`, `nak_nir.c:nak_optimize_nir`, `nvk_shader.c:nvk_physical_device_compiler_flags` | **ABSENT**: P1 retains texture/global-group estimates 32, peephole limit 0, compiler ABI 10 with no Switch bit at 24. Patch uses 200 before SM70, limit 8, and a Switch cache revision bit. The changed group includes more than loads (e.g. atomics/barriers). | No semantic P1 dependency. Possible register pressure/spills, scheduler regressions and cache incompatibility. Applies with exit 0 but is excluded from both variants. Historical compiled game cache must not be reused across this compiler difference. Attribution in the patch is not independent authentication of the original nfsmw source. |
| [0003](https://github.com/R-drg/project8-sw/blob/3393f2615deb7e8ac11ce51d2cd0e9defded5669/patches/mesa-switch/0003-nvk-switch-direct-draws-and-memory-query.patch), Project 8; `nvk_cmd_buffer.c:CmdExecuteCommands`, `nvk_cmd_buffer.h:nvk_graphics_state`, `nvk_cmd_draw.c` begin/direct/indexed/multi/indirect draw functions, `os_misc.c:os_get_available_system_memory` | **ABSENT**: P1 has no `switch_draw_params_zero` tracking/direct-draw helper and queries `mallinfo` each request. Patch bypasses the macro for qualifying pre-Pascal zero-parameter draws and retains a memory estimate for 100 ms. | Patch's hunk context expects 0001, but has no semantic dependence on CPU UBO resolution; applies to P1 with exit 0. State invalidation across draws/secondary commands and multiview is correctness-sensitive. Unsynchronized static budget cache is a concurrent access/staleness risk. Excluded from both variants. |
| [0004](https://github.com/R-drg/project8-sw/blob/3393f2615deb7e8ac11ce51d2cd0e9defded5669/patches/mesa-switch/0004-switch-build-script.patch), Project 8; adds `build-switch-p8.sh`, `p8-native.txt`; changes `rustc-switch-wrapper.sh` argument selection | **ABSENT**: P1 has general build scripts/wrappers but no p8 script/native file or proc-macro exception. Patch adds explicit Rust ELF target, host tools, cross flags/archive targets and host proc-macro handling. | Applies with exit 0; not imported. Native mesa_clc/vtn_bindgen2 and a host Rust compiler for proc macros remain requirements for a fresh driver build. Wrapper correction belongs to Mesa owner if needed. Script uses mutable image tags, `--wipe` and removes a named container: unsuitable for preservation without a separate owned build plan. Packaging/indexing remains a consumer responsibility. Build necessity is **NOT YET VERIFIED** with the absent toolchain/archives. New script license provenance needs resolution before copying. |

Relevant exact implementation sources:
[NAK estimates](https://github.com/giusepper11/mesa-switch/blob/fe0282715a329ce2c5702b5893e65cc5e54d7f67/src/nouveau/compiler/nak/opt_instr_sched_common.rs#L284),
[flattening](https://github.com/giusepper11/mesa-switch/blob/fe0282715a329ce2c5702b5893e65cc5e54d7f67/src/nouveau/compiler/nak_nir.c#L206),
[cache flags](https://github.com/giusepper11/mesa-switch/blob/fe0282715a329ce2c5702b5893e65cc5e54d7f67/src/nouveau/vulkan/nvk_shader.c#L72),
[draws](https://github.com/giusepper11/mesa-switch/blob/fe0282715a329ce2c5702b5893e65cc5e54d7f67/src/nouveau/vulkan/nvk_cmd_draw.c#L4969),
[memory query](https://github.com/giusepper11/mesa-switch/blob/fe0282715a329ce2c5702b5893e65cc5e54d7f67/src/util/os_misc.c#L480),
[Rust wrapper](https://github.com/giusepper11/mesa-switch/blob/fe0282715a329ce2c5702b5893e65cc5e54d7f67/rustc-switch-wrapper.sh).

## Integration constraints and existing adapter

Design: one unmodified P1 driver build, one patched ReXGlue SDK, one consumer
binary; separate processes set option 0/1 before Vulkan initialization.
No supporting driver patches are imported. Preserve game code and all settings.
Failure to authenticate archives/link ABI or demonstrate eligible workload
halts the build/measurement path. There is no authority here to convert dynamic
descriptors to fixed ones or broaden the Mesa guard.

Reuse `REXGLUE_SWITCH_NVK_SDK`, the existing regular-AArch64 archive validator
and `switch_mesa_env`; no new build adapter is justified while these gates are
blocked. [The SDK hook](https://github.com/R-drg/project8-sw/blob/3393f2615deb7e8ac11ce51d2cd0e9defded5669/patches/rexglue-sdk/0009-switch-render-target-cuts.patch#L1255)
sets variables in SetupEnvironment before initialization. P1 calls
`debug_get_bool_option` at qualifying command recording, which calls
`os_get_option`/`getenv` directly (not its cached counterpart):
[option parser](https://github.com/giusepper11/mesa-switch/blob/fe0282715a329ce2c5702b5893e65cc5e54d7f67/src/util/u_debug.c#L197),
[environment access](https://github.com/giusepper11/mesa-switch/blob/fe0282715a329ce2c5702b5893e65cc5e54d7f67/src/util/os_misc.c#L240).
This is not permission to hot-switch; recorded command buffers retain prior
bindings and concurrent environment mutation is unreviewed. Processes must exit
between variants. [protocol.md](protocol.md) specifies the pending configuration.

Driver SDK must contain `lib/libvulkan.a` and, if not included in the merge,
`lib/libnak_rs.a`. SDK patch 0008 links optional NAK plus repeated
`vulkan expat zstd z nx` and `stdc++ m`. The
[existing staging script](https://github.com/R-drg/project8-sw/blob/3393f2615deb7e8ac11ce51d2cd0e9defded5669/tools/switch/stage-mesa-archive.py)
checks a regular archive, ELF64 little-endian/AArch64 members, then target ranlib;
it does not authenticate source revision, compiler flags, symbol completeness
or ABI. Before any build, retain a clean-source proof, exact driver build command,
toolchain/image digest, object/library hashes and archive construction manifest.
Do not use the old 26.2.3 patched SDK as P1. A complete final link and undefined
symbol audit are required; archive shape alone cannot clear this gate.

The game toolchain defines Switch/NX, Vulkan, static library try-compiles, PIE,
local-exec TLS and Cortex-A57 flags. Its Dockerfile requires devkitA64/libnx,
clang-19/glslang but pins only `latest`; freeze an actual image digest before
building. The host code generator requires CMake >=3.25/Ninja/clang, the pinned
SDK and submodules. All 16 port SDK patches are shared prerequisites.
Native translation requires the locally owned Xbox 360 `default.xex` SHA-256
`cfc732340e55defda400e25f03231aa9bb65fd9545b618212f69a4952384a5dd`
and the port manifest. Runtime also needs extracted DATA; no complete asset
inventory hashes are provided by this source, so retain a local manifest.
Generated `sources.cmake`, `thps_p8_pch.h`, `thps_p8_init.h` and translation
units must stay private. NRO packaging uses SDK `elf2nro`, NACP and game icon;
retain unstripped ELF/build ID and final NRO hashes. No such artifacts exist
for this integration. GPL runtime source and BSD/MIT dependencies have notices;
FFmpeg/libmspack static LGPL relinking obligations and full dependency notices
remain release requirements. This preparation publishes no executable.

## Validation and evidence

[commands.json](evidence/commands.json) retains argv, working directory, exits
and full output per check. [input-hashes.json](evidence/input-hashes.json)
authenticates inspected patches, release config and staging script. Source pins
authenticate public source only. No NRO, ELF, driver archive or hardware hash
was produced.

| Check | Actual outcome |
| --- | --- |
| Mesa ancestry and baseline-to-P1 diff | exit 0; P1 directly descends from baseline |
| Mesa patch apply checks only | 0001 exit 1 (expected conflict); 0002–0004 exit 0; none applied |
| All 16 ReXGlue patches, fresh public clone | each exit 0; SDK diff check exit 0; submodules/build unverified |
| Patch-series inventory / no-game-content gates | exits 0 / 0 |
| Existing guest render-wait test | exit 1: missing `c++`; not a PASS and not modified |
| CMake / Ninja inventory | launch unavailable, recorded 127; no build attempted |
| Docker image inventory | exit 1: socket permission denied; no image inspected |
| Generated translation / devkitA64+libnx prerequisites | exits 1 / 1: absent |
| Descriptor-flow source searches | exits 0; five dynamic bindings identified |
| Game link, archive/ABI validation, GPL binary boundary, NRO packaging | NOT RUN: required inputs absent |

Initial network checks failed DNS in the sandbox; public checks/clones succeeded
after network escalation. Fork request with an organization parameter failed
422 because giusepper11 is a personal account; the corrected personal request
succeeded, without a duplicate repository. The first ledger collection stopped
on missing CMake after preserving logs 00–36. Ledger recovery retained those
results and recorded unavailable executables explicitly; no failed test was
rewritten as PASS. Final diff/content checks are retained in delivery evidence.

The earlier four-draw fragment UBO observations are [bounded historical evidence](https://github.com/giusepper11/switch-research-knowledge/blob/983d01e6fdc19e108c03dfa5529da8864df9e7f1/experiments/mesa-fragment-ubo-2026-10-11/hardware-evidence/comparison.json),
not Project 8 compatibility or performance evidence. Their route observer is
excluded from performance timing. No console operation or hardware test occurred.

## Risk register and next discriminator

| Risk / gate | Status and smallest resolution |
| --- | --- |
| Primary game UBO workload does not enter P1 | BLOCKED; source flow supports unchanged dynamic path. Locate a genuine ordinary fixed fragment binding in existing Free Skate/helper source before considering runtime instrumentation. Do not alter the renderer to manufacture eligibility. |
| Legitimate assets/generated code absent | BLOCKED for full build; only locally obtained supported inputs may clear it. |
| Archive build provenance, ABI and toolchain missing | BLOCKED; authenticate one clean P1 SDK and complete a legitimate consumer link before hardware approval. |
| Supporting patch license / 0004 wrapper necessity | BLOCKED for copying; no copied code proposed. Mesa owner must handle required driver/toolchain corrections. |
| Compiler/cache migration from game release | Unverified; isolate caches and preserve originals. Never reuse historical NAK cache based on driverVersion filename alone. |
| Deterministic Free Skate scene / route selection | Unverified; freeze location/save/camera/input and record per-window work counts. No scene identity is invented here. |
| CPU timing, route counts, per-frame timestamps and faults | Unverified; existing aggregate logs are insufficient. Calibrate timer/probe overhead and retain failed runs. |
| HOS, clocks, correctness, stability and performance | Unknown; require separately approved hardware evidence. No gain is claimed. |

Next action is a read-only eligibility audit of remaining SDK/UI/helper paths
that actually run in native Free Skate. If no relevant ordinary UBO exists,
retain Project 8 as a possible no-op control and choose a naturally eligible
consumer in a separately scoped task. This fork owns validation preparation
only; all optimization ownership remains in `giusepper11/mesa-switch`.
