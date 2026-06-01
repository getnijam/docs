---
title: CI variables
description: Every environment variable the reporter reads to attribute a run.
sidebar:
  order: 2
---

The reporter auto-detects run metadata from CI environment variables, falling back to generic variables
and then `git`. This page lists every variable it reads, per provider. For the narrative version, see
[CI integration](/reporter/ci-integration/).

## Resolution order

For each field (commit, branch, PR, run id, run URL, author):

1. **CI-specific** variable (tables below)
2. **Generic** variable (`GIT_*`, `BRANCH`, `COMMIT_SHA`, …)
3. **`git`** shell-out (`git log` / `git rev-parse`)
4. **Empty** (branch left unset → dashboard shows "No Branch Info")

## GitHub Actions

| Field      | Variable                            |
| ---------- | ----------------------------------- |
| Commit     | `GITHUB_SHA`                        |
| Branch     | `GITHUB_REF_NAME` / `GITHUB_HEAD_REF` |
| Run id     | `GITHUB_RUN_ID`                     |
| Repository | `GITHUB_REPOSITORY`                 |
| Server URL | `GITHUB_SERVER_URL`                 |

GitHub doesn't expose a commit-author email → falls back to `git`.

## GitLab CI

| Field        | Variable                  |
| ------------ | ------------------------- |
| Commit       | `CI_COMMIT_SHA`           |
| Branch       | `CI_COMMIT_REF_NAME`      |
| Pipeline id  | `CI_PIPELINE_ID`          |
| PR (MR) id   | `CI_MERGE_REQUEST_IID`    |
| Author email | `GITLAB_USER_EMAIL`       |
| Author name  | `GITLAB_USER_NAME` / `CI_COMMIT_AUTHOR` |

## CircleCI

| Field   | Variable              |
| ------- | --------------------- |
| Commit  | `CIRCLE_SHA1`         |
| Branch  | `CIRCLE_BRANCH`       |
| Build # | `CIRCLE_BUILD_NUM`    |
| PR      | `CIRCLE_PULL_REQUEST` |
| Build URL | `CIRCLE_BUILD_URL`  |

CircleCI doesn't expose a commit-author email → falls back to `git`.

## Bitbucket Pipelines

| Field      | Variable                    |
| ---------- | --------------------------- |
| Commit     | `BITBUCKET_COMMIT`          |
| Branch     | `BITBUCKET_BRANCH`          |
| PR id      | `BITBUCKET_PR_ID`           |
| Build #    | `BITBUCKET_BUILD_NUMBER`    |
| Repository | `BITBUCKET_REPO_FULL_NAME`  |
| Origin URL | `BITBUCKET_GIT_HTTP_ORIGIN` |

Bitbucket doesn't expose a commit-author email → falls back to `git`.

## Generic (any CI)

Set these on unsupported providers and the reporter picks them up:

| Field     | Variable     |
| --------- | ------------ |
| Commit    | `COMMIT_SHA` |
| Branch    | `BRANCH`     |
| Run id    | `CI_RUN_ID`  |
| Run URL   | `CI_URL`     |

## Git fallback

When no CI or generic variables are set, the reporter resolves commit/branch/author from the local
repository via `git log -1`, `git rev-parse`, and `git config user.email`/`user.name` — so even local runs
are attributed.
