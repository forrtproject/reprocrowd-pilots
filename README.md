# ReproCrowd pilots

Public, **unreviewed** records of computational reproductions made with the [ReproCrowd app](https://github.com/forrtproject/reprocrowd-app). Each record is a place to inspect and discuss a reproduction before anything is archived with a DOI on Zenodo.

> [!WARNING]
> A pilot record is not peer review, a certificate of a study's validity, or an endorsement. When the numbers match at the reported precision, that shows only that the posted analysis code produced the printed numbers from the posted inputs.

## How a record is organised

- **One issue per reproduction.** The issue has the labels `reprocrowd-pilot` and `unreviewed` once its first run is copied. Its description shows the latest run and a table of all runs. Independent reproductions of the same paper get separate issues; the paper's DOI is metadata, not the record's identity.
- **One pre-release per run.** Each run is tagged `<reproduction-id>-<run-key>`. The app publishes it in the submitter's fork of this repository; this repository's Action (`.github/workflows/pilot-copy.yml`) checks it and copies it here. The copy holds:
  - the package ZIP, a Frictionless data package;
  - `report.html`, `comparison.json`, `execution.json` and `licenses.json`, copied out of that ZIP;
  - `pilot.json`, a machine-readable summary with the SHA-256 hash of every file.
- **One comment per run** on the issue, with the summary, the comparison and links to the files with their hashes.
- **Runs are numbered v1, v2, …** by the Action when it copies them. Later runs are added as new versions. Published releases are immutable on this repository, so earlier runs stay exactly as they were.
- **The run key is computed from the archive's hash** (and which file groups were left out). Submitting the same archive again changes nothing: the app finds the existing issue, release and comment and creates no duplicates. If an upload fails partway, submitting again resumes it.

Issues labelled `synthetic` are test fixtures of the publication workflow, not research results.

## Licences

| What | Licence |
|---|---|
| Metadata: `datapackage.json`, targets, plan, `pilot.json`, `licenses.json` | [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/) |
| Newly authored reports, comparisons, execution evidence, figures and analysis outputs | [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) |
| Newly authored analysis code (the master script and other new code) | [MIT](https://opensource.org/license/mit) (each run's `licenses.json` states it) |
| The article's text, extractions and original figures; the input data and code | Not relicensed: they keep their original licences |

Every run's `licenses.json` lists the licence of each file. Our licences cover only our own contribution. Passages of the article quoted in our files (targets, plan, report) remain under the article's terms. Outputs computed from third-party data are also subject to that data's terms. By default the article's own material is left out of the ZIP unless OpenAlex reports an open licence for it; `excluded-files.json` lists what was left out, with hashes. This repository's own text (this README, the templates) is CC BY 4.0.

## Submitting a run

Anyone with a GitHub account can submit; you don't need access to this repository. On your first submission the app forks this repository into your account and publishes each run's files as a release there. It opens the reproduction's issue here and posts a comment for the run. This repository's Action then copies the files into its own release and replies on the issue.

The Action copies a run only if the comment is by the issue's author (or a maintainer), the files come from the commenter's own fork, no other issue holds a copied run of the reproduction, the run's files total at most 200 MiB, and every file matches its SHA-256 in `pilot.json` and GitHub's digest. Otherwise it replies with the reason and copies nothing. Maintainers can rerun it for an issue under **Actions → Copy pilot runs → Run workflow**.

1. Log in to GitHub once with the [GitHub CLI](https://cli.github.com): run `gh auth login` in a terminal, choose GitHub.com and log in with your browser. ReproCrowd uses that login. In **Settings → GitHub pilots**, **Log in with gh** starts it for you.
2. In **Settings → GitHub pilots**, click **Check**. It should say that your account can submit and where your files go.
3. In the ReproCrowd app, finish the report and create the ZIP (step 8).
4. In step 8, click **Submit this run as a GitHub pilot**.

From the command line, the same service does the same thing:

```sh
gh auth login                           # once; or a classic token with public_repo: export REPROCROWD_GITHUB_TOKEN=…
uv run --project backend rc pilot check
uv run --project backend rc pilot preview <package-id>
uv run --project backend rc pilot submit <package-id>
```

The app refuses a submission when the report is out of date (`report_stale`, for example after a new run), when the last run failed, or when the paper's text came from a local file rather than from its DOI.

## Reviewing

Comment on the issue. Do not edit the release files; they are the record. A review process (trust levels, moderation, signing) has not been defined yet.
