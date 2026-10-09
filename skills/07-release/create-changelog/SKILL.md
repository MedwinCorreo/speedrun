---
name: create-changelog
description: Consolidate changes across repository main branches into evidence-backed public Markdown changelog drafts following Keep a Changelog. Use for cross-repository release notes scoped to today, a specific day, a date range, or a previous changelog.
---

# Create changelog

Disciplines: DevOps

Produce one public Markdown draft describing product outcomes across the supplied
repositories, following [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
with a separate private evidence record. Drafting does not authorize publishing,
committing, or pushing.

Requires Git, read access to the supplied repositories, and permission to fetch
their selected remotes. Verified merge timestamps may require authenticated
GitHub or GitLab access through an available CLI, API, or connector. No specific
coding agent is required; `agents/openai.yaml` supplies optional Codex UI metadata.

## Inputs

Establish the repository paths and their surface labels (for example backend,
mobile app, and dashboard), reporting scope, timezone, audience, and destination
from the request or explicit workspace instructions. Use `main` as the target
branch unless the user specifies another branch; never substitute another branch
when `main` is missing. Ask for missing inputs that affect the result. If no
destination is given, return the public draft and private evidence as separately labeled
sections in the conversation; ask before saving them to disk.

Accept these scopes:

- **Today:** midnight through the collection time in the supplied timezone.
- **Specific day:** midnight on that day through midnight on the following day.
- **Date range:** include both named calendar dates, using midnight on the first
  date through midnight after the last date.
- **Explicit timestamps:** use the supplied start inclusive and end exclusive.
- **Since a previous changelog:** use its accepted evidence record, which must
  contain one full baseline SHA per repository. A first run needs dates or SHAs.

Use start-inclusive, end-exclusive intervals and record the resolved timestamps
with UTC offsets. For "today," use the actual collection date in that timezone,
not the date of an older conversation. Ask if a range is ambiguous or inverted;
flag future dates rather than treating them as collected history.

For a supplied dashboard or other operator surface, clarify whether the public
audience includes operator-facing outcomes. Review that surface either way,
since it may support a public app feature.
Distinguish a summary of merged work from released/available changes. Main-branch
history alone cannot prove deployment or app-store availability.

## Repositories and authority

Use only repositories supplied by the user or explicitly identified in workspace
instructions. Resolve relative paths against the current workspace and verify
each checkout. Ask for missing repositories rather than guessing directory names.

Read each repository's `AGENTS.md` when present and follow pointers to canonical
product documentation. Use that documentation for terminology and intent; verify
implementation in source and tests, and availability in release evidence. The
skill does not require a particular framework, hosting provider, or code graph.

## Collect exact main-branch evidence

1. Verify each checkout and its configured remote belong to the expected repo.
   Fetch the selected remote and target branch into its remote-tracking ref,
   respecting
   execution permissions. Pin the resulting full SHA for the entire run. Keep
   working files and the current branch intact. If fetching fails, report it and
   ask before using a stale local snapshot. Do not substitute a feature branch.
2. For SHA baselines, verify each baseline exists and is an ancestor of its
   pinned head. Compare `BASE..HEAD` separately in each repo. Missing history or
   a rewritten branch needs clarification, not a guessed replacement baseline.
3. For dates, identify changes integrated into `main` within the interval, not
   feature commits authored within it. Inspect first-parent integrations and
   include their full diffs, including older commits brought in by merges.
   Prefer verified GitHub pull-request or GitLab merge-request merge timestamps
   when accessible, matching them to integrations reachable from the pinned target
   branch. Check direct commits too. Git committer timestamps are only a proxy for
   arrival on main, especially with fast-forward merges or delayed pushes: ask
   before using that fallback and label its limits in the evidence record.
   Do not claim exact integration dates when the evidence cannot establish them.
4. Inspect affected source, existing relevant tests, and contract docs at pinned
   revisions. Commit subjects and merge-request descriptions are discovery hints,
   not implementation proof. Use revision-based reads when a checkout is on
   another branch. Use repository navigation tools when required by its
   instructions, then verify claims against pinned Git trees.

Useful Git operations, substituting verified paths, remotes, branch names
(`main` by default), and revisions:

```sh
git -C REPO remote -v
git -C REPO fetch REMOTE refs/heads/BRANCH:refs/remotes/REMOTE/BRANCH
git -C REPO rev-parse refs/remotes/REMOTE/BRANCH
git -C REPO merge-base --is-ancestor BASE HEAD
git -C REPO log --first-parent --format='%H %cI %s' BASE..HEAD
git -C REPO diff INTEGRATION^1 INTEGRATION
git -C REPO show HEAD:path/to/file
```

For a date-scoped run without a baseline, inspect first-parent history of the
pinned head and resolve interval membership using the selected timestamp source.
Avoid relying solely on `git log --since` or author dates: they can omit older
commits merged during the interval. For root commits, inspect the commit directly.

Review final behavior at pinned heads: reverted or superseded changes must not
appear as current improvements. Track every integration candidate as included,
deferred, or omitted with a reason. Record a repository with no changes; an
inaccessible repository makes the consolidation partial.

## Consolidate public outcomes

- Group changes by user outcome. One feature spanning multiple repositories gets
  one public entry with all supporting evidence recorded privately.
- Check every surface needed by a claim, even when only one changed in the
  interval. An endpoint alone does not prove the app exposes a feature. Dashboard
  actions need their backend contract and authorization. Defer incomplete
  outcomes and record alignment gaps privately.
- Describe concrete benefits using the Keep a Changelog categories defined below.
  If operators are included in the audience, label operator-specific bullets
  within those categories, rather than adding another change-type heading.
- Omit internal refactors, dependencies, generated graphs, tests-only work, and
  docs-only work unless they produce a supported outcome relevant to the audience.
  Avoid speculative performance or reliability claims.
- Keep secrets, personal data, internal hostnames, private links, permission
  internals, and exploit details out of public copy. Describe supported security
  improvements at a safe user-facing level.
- Put merged work under `## [Unreleased]` and label the draft **Availability not
  verified**. Released wording requires deployment/app-release evidence or
  explicit user confirmation covering the claimed outcomes. Defer unreleased
  outcomes from a released changelog. Never invent a version or rollout date.
- If no qualifying public changes exist, state that plainly. Do not fill the
  changelog with internal work to make it look substantive.

## Keep a Changelog output convention

Use `# Changelog` with these change categories, omitting empty categories:

| Heading | Use for |
| --- | --- |
| `### Added` | New features |
| `### Changed` | Changes to existing functionality |
| `### Deprecated` | Features scheduled for removal |
| `### Removed` | Removed features |
| `### Fixed` | Bug fixes |
| `### Security` | Security fixes described without exploit details |

Retain consequential breaking changes, deprecations, and removals for the chosen
public audience. State required user action when supported by the evidence.

Keep `## [Unreleased]` above released versions. A released entry uses
`## [VERSION] - YYYY-MM-DD`, with the actual release date rather than the
collection date. Order releases newest first. Ask for a product release identifier
and the corresponding source revisions when producing a released entry; independent
repository versions do not establish a shared product version. Inspect behavior at
those released revisions, not at newer main heads. Record both snapshots privately.

Make version headings linkable using public release or comparison links when
available. For a multi-repository product, use a verified public product-release
link rather than an invented cross-repository comparison URL. Keep private links
in the evidence record. If public links are unavailable, disclose that limitation.

State whether the project uses [Semantic Versioning](https://semver.org/) only
when its policy is known; ask when preparing a persistent changelog and it is
unknown. Never infer a version bump from commit prefixes.

A merged-work draft can begin:

```markdown
# Changelog

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

Draft. Availability not verified.

### Added

- A supported new user capability.

### Fixed

- A supported correction to user-visible behavior.
```

The example bullets are placeholders for this illustration, not generated claims.
Record the reviewed date scope in the draft or its accompanying summary; the date
filter is a collection input, not a release date. When editing an existing
`CHANGELOG.md`, preserve previous entries and merge qualifying outcomes into the
correct section without duplicating them. Use `CHANGELOG.md` as the conventional
filename when the user requests a saved changelog without specifying a filename.

## Outputs and repeat runs

The public draft contains its date scope, verified status, and outcome bullets.
The separate private evidence record contains:

- Resolved interval and timezone, audience, collection time, repository paths,
  selected remotes, baseline SHAs when used, and full pinned head SHAs.
- Timestamp source and limitations; each public entry mapped to commits, source
  paths, tests inspected, and release evidence when claimed. Distinguish tests
  inspected from tests actually run; tests at another revision are not head proof.
- Deferred outcomes with original evidence and remaining requirements; omitted
  candidates with reasons; unavailable sources and other verification limits.
- Whether the user accepted the draft as the next baseline.

For a date-scoped draft, record a separate proposed baseline SHA per repository
at the reporting interval's upper boundary. A collected head newer than that
boundary is not a valid next baseline: it would skip later changes. If the
boundary revision cannot be verified, leave the baseline unset and require an
explicit baseline or date range on the next run.

Save files only at requested locations, keeping private evidence outside publicly
served directories. If filesystem permissions block writing, request the required
approval or return the prepared content with the limitation stated.

For repeat runs, use the latest accepted baseline and recheck deferred outcomes,
even when their original commits fall outside the new interval. Generating a draft
does not advance the accepted baseline. Carry unresolved outcomes until resolved
or explicitly dismissed.

Before finishing, verify every public bullet has evidence, duplicate outcomes are
merged, superseded behavior is removed, all supplied repositories are accounted for,
and public wording matches verified status and the output convention. Report
output locations when saved and remaining uncertainty. Publishing requires a separate explicit instruction.

## Invocation examples

```text
Use $create-changelog for today in Asia/Manila.
Repositories: backend=./api, mobile=./app, dashboard=./dashboard.
Audience: app users. Return a public draft and private evidence in chat.
```

```text
Use $create-changelog for October 1–9, 2026, inclusive, in Asia/Manila.
Repositories: backend=./api, mobile=./app, dashboard=./dashboard.
Audience: app users and operators. Summarize merged work.
Save the public draft to PUBLIC_PATH and evidence to PRIVATE_PATH.
```

```text
Use $create-changelog since the accepted evidence record at PRIVATE_PATH.
Use its recorded repository paths after verifying they are accessible.
Save the new public draft to PUBLIC_PATH and evidence to NEW_PRIVATE_PATH.
```
