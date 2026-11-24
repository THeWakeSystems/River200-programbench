# River-200-V1-FP8 · ProgramBench

## System overview

This submission evaluates River-200-V1-FP8 served through a self-hosted OpenAI-compatible API with mini-swe-agent 2.4.6 and its ProgramBench agent. The agent used Bash inside each task's official Docker cleanroom with image tag `task_cleanroom_v6` and networking disabled. The agent configuration recorded in all 200 trajectories specifies a 1,000-step limit, a 21,600-second wall-clock limit, and a 180-second per-command timeout. The client configurations defer sampling and generation settings to the server. Each task has one mini-swe-agent `traj.json`, with its prompt and configuration, plus the final solution archive and native grader output where available.

## Reproducing the evaluation

The original grading used ProgramBench 1.2.5 and the official task data at revision `5954a1957b5e0d770ec2c5523506abd212806315`. Inference used the ProgramBench entry point in mini-swe-agent 2.4.6 (`mini-extra programbench`) with the model and task settings in the trajectories. Reproducing inference also requires access to the same model snapshot and serving configuration.

### Downloading artifacts and verifying results

The GitHub result repository uses Git LFS for the 199 solution archives (`submission.tar.gz`) and 199 detailed grader logs (`*.eval.log.json`). Per-task scores and agent trajectories are stored as regular Git files.

From the root of a clone of the result repository, run Tier-0 to check the recorded scores.

```bash
uvx --from programbench==1.2.5 programbench submit verify .
```

For Tier-1, install Git LFS and run these commands from the repository root. `git lfs pull` downloads the actual archives and logs into the checkout. Tier-1 also requires Docker and the official task images and test data.

```bash
git lfs install --local
git lfs pull
uvx --from programbench==1.2.5 programbench submit verify . --tier1
```

Tier-0 passed all 199 scored instances. The 200-instance macro-average is 40.78264093%. On the leaderboard this rounds to 40.8%, with 2/200 fully resolved and 18/200 at or above 95%.

## Time limits and score coverage

| Instance | Recorded outcome |
| --- | --- |
| `stathissideris__ditaa.f2286c4` | The official grader hit its 3,600-second deadline on branch `5695bac60382`. The candidate stalled in the vertical scan of a PNG case. The native result preserves 0/609 passed and 601 tests marked `not_run`. |
| `sqlite__sqlite.839433d` | The official grader hit its 3,600-second deadline on branch `4ee128ddea8e`. Console progress reached 99%, but the branch did not yield its JUnit results. The native result preserves 105/13,510 passed and 13,245 tests marked `not_run`. |
| `zk-org__zk.10d93d5` | The solver reached its time limit and produced no valid submission. The trajectory is included, and the instance contributes zero to the 200-task score. |
| `wfxr__csview.8ac4de0` | The solver reached its time limit. The archived submission lacks `compile.sh`; the official grader reported `compile_failed` and 0/335 passed. |

The remaining native grading flags and the solver exit status for every task are preserved in the per-task artifacts. Tier-1 Docker reproduction of this package has not been run.

## Registration

The ProgramBench registration PR will reference the GitHub result repository and pin the commit that contains the evaluation artifacts.

After initializing the result repository, configure Git LFS before staging the evaluation artifacts. Commit the generated `.gitattributes` with the submission.

```bash
git lfs install --local
git lfs track 'submission.tar.gz' '*.eval.log.json'
git add .gitattributes
```

## Integrity attestations

- [x] Solutions were produced **only** from behavioral observation of the binary and its
      bundled docs — no source code, repositories, mirrors, or package registries were consulted
- [x] The model was not given internet access during evaluation
- [x] The model did not have access to any unit tests during evaluation
- [x] I consent to re-evaluation, and to flagging or removal if it contradicts the reported results
