# Issue tracker: GitHub

The canonical tracker is [marcodinf-a11y/qfjk](https://github.com/marcodinf-a11y/qfjk/issues). Use `gh` for issue operations. Historical `.scratch/` files are local migration sources, not live tickets.

The current map is [Chart qfjk's first playable release](https://github.com/marcodinf-a11y/qfjk/issues/1).

## Wayfinding operations

- Find the map by label `wayfinder:map`. Its body holds Destination, Notes, Decisions so far, Not yet specified, and Out of scope.
- Decision tickets are native GitHub sub-issues of the map, labelled `wayfinder:research`, `wayfinder:prototype`, `wayfinder:grilling`, or `wayfinder:task`. Refer to issues by linked title in human-facing text.
- Dependencies use GitHub's native `blocked_by` relationship. Add a dependency with `gh api --method POST repos/marcodinf-a11y/qfjk/issues/<child-number>/dependencies/blocked_by -F issue_id=<blocker-database-id>`. Use the numeric REST `id`, not its issue number or GraphQL `node_id`.
- Query children with `gh api --paginate repos/marcodinf-a11y/qfjk/issues/<map-number>/sub_issues`. The frontier consists of open, unassigned children with `issue_dependencies_summary.blocked_by == 0`; choose the first in map order. When that summary is absent, query the child's `dependencies/blocked_by` endpoint and check each blocker's state.
- Claim a frontier ticket before work with `gh issue edit <number> --repo marcodinf-a11y/qfjk --add-assignee @me`.
- Resolve by posting the answer as a resolution comment, closing the issue, then adding a one-line gist and named link to the map's Decisions so far. Preserve the original question in the issue body. Create newly sharp tickets before wiring their dependencies.
- Research artifacts live in `docs/research/` and are linked from resolution comments. Human decisions require the user's participation; documentary research is not proof of a completed platform test.
- Character-sheet PDFs are private local inputs. Keep character, player, and companion names and identifying source filenames out of GitHub issues, comments, and committed artifacts. Use class or role labels in user-confirmed inventory summaries. Root-level PDFs are gitignored.

The map indexes decisions; the resolution comment on each ticket holds its answer. Open work is discovered through sub-issues and dependencies rather than duplicated in the map body.
