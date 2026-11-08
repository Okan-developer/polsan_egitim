# Git

> Bootstrap 2026-11-02 ile ayarlandı. Varsayılan dal `main`; değişiklikler yalnız PR ile girer.

## Branching
- `feat/<spec-no>-<short-name>` — **no branch without a spec.** Also for CHANGE-lane mini-specs.
- Refactors: `refactor/<spec-no>-<short-name>` (mini-spec with Changed behavior = none).
- Fixes: `fix/<report-id>-<short-name>` (bug fixes carry a report, not a spec number);
  incidents: `incident/<date>-<short-name>`; trivial changes: `trivial/<short-name>` (see below).
- Bootstrap: `chore/bootstrap` — the bootstrap output (`workflows/bootstrap.md`), merged like
  any branch.

## Commits
- Conventional Commits, with a plan reference: `feat(catalog): paging endpoint [plan 0001/3]`.
  Review fixes name their fix round instead of the step:
  `fix(catalog): empty page size [plan 0001/fix-2]` — the reviewer counts rounds from these
  (R-06 after round 3).
- Commit mesajının açıklaması Türkçe olabilir; tip ve kapsam İngilizce kalır (`docs/conventions.md`, Language).
- Agent commits follow the same standard: the agent writes the message, the human approves it
  through the gate before the commit, not one by one — an approved plan covers its
  `[plan NNNN/n]` and `[plan NNNN/fix-k]` commits, the ship approval the ship commit. Every
  message stays visible in the branch and the PR. Lite and strict alike.
- Commit named paths only: `git commit -- <paths>`, never `git add -A`, `git add .` or
  `commit -a`. Another session or tool may have written or staged files in the same tree.

## Forbidden
- Direct commits to the default branch.
- Derlenmiş çıktı (`out/`, `target/`, `*.class`) commit edilmez (`.gitignore`).
- Force push, history rewriting on shared branches. Undo = `git revert` (see recovery R-11).

## Trivial changes
<!-- Set at bootstrap. Applies only to requests the change-request triage rubric
     (workflows/change-request.md) classifies as TRIVIAL: no acceptance criterion changes, no shared code. -->
- Policy: **(1) PR + one reviewer, no spec** — bootstrap 2026-11-02 kararı. (Seçilmeyenler: (2) `trivial/` dalında doğrudan commit · (3) her şey mini-spec.)
- Hassas alanlara (`docs/domain.md`, Sensitive areas) dokunan hiçbir değişiklik trivial sayılmaz — `/change` akışı.
- Always: one narrow commit, `scripts/check` green. A second file or a new test means re-triage.

## Remote & push
- **Remote:** `origin` = `https://github.com/Okan-developer/polsan_egitim.git` (2026-11-08 kararı;
  2026-11-02'deki "remote yok" kararının yerini alır). Repo-yerel `http.sslBackend=schannel` gerekir
  (kurumsal sertifika).
- **Push yalnızca kullanıcı açıkça istediğinde** yapılır; agent kendiliğinden push etmez. Force-push yok.
- Full yeşil kanıtı yerel `./scripts/check` (full) çalıştırmasıdır (`docs/testing.md`, Two speeds kural 6); CI yok.

## Pull requests
- PR akışı kullanılmıyor: "PR + reviewer" = bağımsız review oturumu (`/review`) + gate onayı;
  `.github/pull_request_template.md` kontrol listesi SHIP gate'inde uygulanır.
- Birleştirme: gate onayından sonra dal yerelde `main`'e alınır (`git merge --ff-only`, gerekirse `--squash`).
