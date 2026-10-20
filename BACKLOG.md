# Backlog

## Publishing
- [ ] Push the first commit (repository created, remote set).
- [x] MIT licence added 2026-10-20.
- [x] Linked from the dashboard's `/setup` page (2026-10-20, in the uncommitted working tree).

## Keeping it in sync
- [ ] Decide the mirror step: copy `savedvars_watcher.py`, `log_watcher.py`, `requirements-watcher.txt` and `tests/test_watcher_startup.py` from the dashboard repo and commit here whenever the exe is rebuilt.
- [ ] Record which exe build is on the server's `/app/downloads` mount, so a reviewer can match the code here to the file they downloaded.

## Carried over from the dashboard backlog
- [ ] Sign the exe (deferred; needed only if a player with Windows 11 Smart App Control cannot run it).
- [ ] Send the watcher version with each payload so the operator can see who is on an old build.
