# Preparation command record

Captured 2026-10-11. These operations precede the per-command audit ledger.
No console commands occurred. Shell groups below retain their actual aggregate
exit; individual commands without a retained exit are not assigned one.

```sh
git status --short
git branch -avv
git remote -v
git worktree list
```

Research checkout was clean at `983d01e6fdc19e108c03dfa5529da8864df9e7f1`,
on `codex/mesa-fragment-ubo-review` with matching origin tracking head;
origin `git@github.com:giusepper11/switch-research-knowledge.git`.
One working tree. `context.md`, `roadmap.md`, `sources.lock.yaml` and the P1
readiness/comparison files were inspected without edits. Measurement contract
read with `git show origin/proposal/mesa-switch-shared-performance:proposals/mesa-switch-measurement-contract.md`, exit 0 in its read group.

Initial sandbox group:

```sh
gh api repos/giusepper11/project8-sw
git ls-remote https://github.com/R-drg/project8-sw.git HEAD
git ls-remote https://github.com/giusepper11/mesa-switch.git HEAD
```

Aggregate exit 128: API connection error and git DNS failures. Network-permitted
retry of the same group exited 0: fork check HTTP 404, game HEAD
`3393f2615deb7e8ac11ce51d2cd0e9defded5669`, Mesa HEAD
`606441c90e459028a50fca352daebfc20211f890`. HTTP 404 was an absence check,
not a successful repository read.

```sh
git clone https://github.com/R-drg/project8-sw.git /tmp/project8-p1-source
git clone --no-checkout https://github.com/giusepper11/mesa-switch.git /tmp/project8-p1-mesa
git fetch origin
gh api repos/giusepper11/mesa-switch/pulls/1 --jq '{head:.head.sha,base:.base.sha,state,draft}'
gh api repos/giusepper11/switch-research-knowledge/pulls/22 --jq '{head:.head.sha,base:.base.sha,state,draft}'
```

Group exit 0. The research fetch updated refs only, with no working-file change.
Mesa PR: base 606441c90e459028a50fca352daebfc20211f890, head
fe0282715a329ce2c5702b5893e65cc5e54d7f67, open/draft.
Research PR: base f17ec0048190b018735d359c93e7acb664b0ce6a,
head 983d01e6fdc19e108c03dfa5529da8864df9e7f1, open/draft.

```sh
git clone --depth 1 --branch v0.10.0 https://github.com/rexglue/rexglue-sdk.git /tmp/project8-p1-rexglue
gh api repos/R-drg/project8-sw/forks -X POST -f organization=giusepper11
```

SDK clone established f5337cdc947ff6d4c4196737e2c807a48f2a1fc2. Group
exit 1: fork request HTTP 422, personal account cannot be an organization.
Correction:

```sh
gh api user --jq .login
gh api repos/R-drg/project8-sw/forks -X POST --jq '{full_name,html_url,parent:.parent.full_name}'
```

Group exit 0; login giusepper11; created giusepper11/project8-sw with parent
R-drg/project8-sw. No second repository was created.

Isolated checkouts were then selected/branched:

```sh
git -C /tmp/project8-p1-mesa checkout --detach fe0282715a329ce2c5702b5893e65cc5e54d7f67
git -C /tmp/project8-p1-source switch -c experiment/mesa-ubo-p1
git -C /tmp/project8-p1-source remote rename origin upstream
git -C /tmp/project8-p1-source remote add origin https://github.com/giusepper11/project8-sw.git
```

Outputs confirmed HEAD at P1 and branch creation. Later ledger records the
actual checkout/remotes/ancestry. No preexisting checkout was switched.

`rg --files` searches in /home/gro/projects, /home/gro/Games and
/home/gro/Downloads found no default.xex or Mesa libvulkan/libnak archives.
This is scoped local absence, not a claim about every possible storage device.
No asset content was read or requested. The adjacent framegen tree at
f582b1b had untracked adapters/saltynx and research/testing/OpenSpec files;
its main branch and SSH origin were inventoried and left untouched.

The temporary audit runner stopped after logs 00–36 because launching CMake
raised FileNotFoundError (runner exit 1). Its recovery preserved these original
logs/exits, handled unavailable tools as 127, and completed collection with
exit 0. That completion is evidence collection only, not an all-tests PASS.
Exact timestamps of the first 37 executions were not retained; ledger
timestamps for those records describe recovery, as each record states.

Supplementary read-only SDK search:

```sh
rg -n 'VK_DESCRIPTOR_TYPE_UNIFORM_BUFFER' /tmp/project8-p1-rexglue/src -g '*cpp'
rg -n 'VK_DESCRIPTOR_TYPE_UNIFORM_BUFFER' /tmp/project8-p1-source/src/game
```

Both found matches: ordinary UBOs in SDK graphics/vulkan/command_processor.cpp
(emulated Xenos path); only dynamic UBOs in game native_renderer.cpp.
This does not authenticate runtime path selection in any game/helper draw.
