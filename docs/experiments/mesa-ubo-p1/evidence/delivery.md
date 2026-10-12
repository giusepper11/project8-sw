# Draft delivery evidence

Audit commit: `ca6bc7c05dbc6e527db7cb7d176a43ffaeb92be3` on
`experiment/mesa-ubo-p1`. It contains only the consumer audit, pending protocol
and public-source/host-check evidence. No game/driver code or binaries changed.

Initial publication group:

```sh
git push -u origin experiment/mesa-ubo-p1
gh pr create --repo giusepper11/project8-sw --base main --head experiment/mesa-ubo-p1 --draft --title 'Prepare blocked Mesa UBO P1 consumer audit and Free Skate protocol' --body-file /tmp/project8-p1-pr-body.md
```

Group exit 1. Push failed: HTTPS Git could not read Username. PR creation then
failed because the head branch was not yet published. Neither is a build/test
failure; both failed delivery attempts are retained here.

```sh
git -c credential.helper='!gh auth git-credential' push -u origin experiment/mesa-ubo-p1
```

Exit 0: new branch published to `giusepper11/project8-sw`, tracking origin.
No global credential configuration or force push was used.

The same `gh pr create` command then exited 0 and returned
https://github.com/giusepper11/project8-sw/pull/1.
The PR was attached to the Codex task. Verified with:

```sh
gh api repos/giusepper11/project8-sw/pulls/1 --jq '{url:.html_url,head:.head.sha,headRepo:.head.repo.full_name,base:.base.sha,baseRepo:.base.repo.full_name,state,draft}'
gh api repos/giusepper11/project8-sw --jq '{full_name,parent:.parent.full_name,default_branch}'
```

Group exit 0: open/draft; head ca6bc7c05dbc6e527db7cb7d176a43ffaeb92be3,
base 3393f2615deb7e8ac11ce51d2cd0e9defded5669; both repos are
giusepper11/project8-sw. Fork parent R-drg/project8-sw, default branch main.
The following evidence-only commit adds this record; the initial audit remains
unchanged and separately identifiable. No merge was performed.

Post-audit local verification group exited 0: `git status --porcelain` empty,
`git diff --check` empty, HEAD equal to the audit commit, and Python SHA-256
recomputation matched every entry in `sha256.json`. That inventory covers the
original evidence bundle, excluding itself and this subsequent delivery record.
It contains no archive/NRO/ELF identity because none was produced.
