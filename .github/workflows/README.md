# workflows

**Objective:** keep the three datasets current with no personal device and no person, given a commit every 60 days. Each workflow is scheduled at 14:00 UTC (9:00 EST, 10:00 EDT), and GitHub starts some runs late or [drops them](https://huggingface.co/datasets/incrediblecrab/us-crs-research-papers#how-it-stays-current).

**Inputs:** the secret `DATA_GOV_API_KEY`, and each job's OIDC token, which Hugging Face exchanges for a short-lived write token when the dataset lists this repository and workflow as a Trusted Publisher; otherwise the run fails.

**Files:**

- [`pipeline.yml`](pipeline.yml), products: `probe` makes one API request. When needed, `run` syncs; `verify --live` and `squash` follow a commit. `continue` starts the next run when one ran out of budget mid-fetch. `inactivity` fails after 50 days without a commit, before GitHub disables every schedule here at 60. It makes no keepalive commit: GitHub [called](https://github.com/ddev/github-action-add-on-test/issues/46) that a Terms violation.
- [`summaries.yml`](summaries.yml), bill summaries: `run`, then `verify` and `squash` after a commit; `continue` as above.
- [`constitution.yml`](constitution.yml), Constitution Annotated: `run`, then `verify --live` and `squash` after a commit.
- [`test.yml`](test.yml): runs the test suite on every push to `main` and every pull request, with read-only contents and no OIDC token, so it cannot publish.

GitHub reports a failed scheduled run to whoever last changed its cron, by email if their settings allow. A dispatch with `args` runs only a bounded test. `.github/` has no README, which GitHub would show instead of the repository's.
