# Contributing to Lambdaforge

This is the human-facing guide for contributing to any repository in the
[`lambdaf-org`](https://github.com/lambdaf-org) organization. It walks through how to help, the
pull request workflow, the setup and checks for each stack we use, and how we review.

Lambdaforge builds small, sharp tools people actually need. We keep the bar high and the process light.

For the short version that ships inside every repo, see the org-wide
[CONTRIBUTING.md](https://github.com/lambdaf-org/.github/blob/main/CONTRIBUTING.md). Our
[CODE_OF_CONDUCT.md](https://github.com/lambdaf-org/.github/blob/main/CODE_OF_CONDUCT.md) applies
everywhere in the org.

## Ways to help

- **Report a bug.** Open an issue with steps to reproduce, what you expected, and what happened.
- **Propose a feature.** Open an issue that describes the problem first, then your idea. Small, focused proposals land fastest.
- **Send a fix or feature.** Pick up an open issue, comment so we know you are on it, then open a pull request.
- **Improve docs.** READMEs, examples, and this guide are fair game and always welcome.
- **Triage.** Reproduce open bugs, confirm they still happen, and add the missing details.

If you are not sure where to start, open an issue and ask. We are happy to point you at something.

## The workflow: fork, branch, pull request

1. **Fork** the repo to your own account.
2. **Clone** your fork and add the upstream remote:
   ```bash
   git clone https://github.com/<you>/<repo>.git
   cd <repo>
   git remote add upstream https://github.com/lambdaf-org/<repo>.git
   ```
3. **Branch** off the default branch. Use a short, descriptive name:
   ```bash
   git checkout -b fix/short-description
   ```
4. **Make your change.** Keep the diff focused on one thing.
5. **Run the checks** for the stack (build, tests, linter, formatter). See [Per-stack setup and checks](#per-stack-setup-and-checks) below.
6. **Commit** using [Conventional Commits](https://www.conventionalcommits.org) (see next section).
7. **Push** to your fork and **open a pull request** against the default branch. Fill in the template and link the issue it closes.

To keep your branch current while you work:

```bash
git fetch upstream
git rebase upstream/main
```

## Conventional Commits

We use [Conventional Commits](https://www.conventionalcommits.org) for commit messages and pull
request titles. The format is `type: short summary in the imperative`.

Types we use:

- `feat:` a new feature
- `fix:` a bug fix
- `docs:` documentation only
- `refactor:` a code change that does not add a feature or fix a bug
- `test:` adding or fixing tests
- `chore:` build, tooling, or housekeeping
- `perf:` a performance improvement

Examples:

```text
feat: add --json output to the translate command
fix: handle empty input file without panic
docs: document the config.yaml fields for synthesis
```

Keep the summary under about 70 characters. Put extra detail in the commit body.

## Per-stack setup and checks

The org runs several stacks. Find the repo you are working on, install the toolchain, then run
the four checks for that stack before you open a pull request.

### Rust

Repos: `auspex-v1`, `clock`, `transl_cli` (and the `neuro` backend under `backend/`).

Install Rust with [rustup](https://rustup.rs). Then from the repo root:

```bash
cargo build              # compile
cargo test               # run the test suite
cargo fmt --all          # format the code
cargo clippy --all-targets --all-features -- -D warnings   # lint
```

Run all four before you push. `cargo fmt` rewrites files in place; run `cargo fmt --all -- --check`
if you only want to confirm formatting without changing files.

For the `neuro` backend, run these from the `backend/` directory.

### Python

Repos: `synthesis`.

Use Python 3. Create a virtual environment, activate it, and install the requirements:

```bash
python3 -m venv .venv
source .venv/bin/activate        # on Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

`synthesis` reads documents or live audio through the Anthropic API, so set your key before you run:

```bash
export ANTHROPIC_API_KEY=sk-ant-...
python3 src/run.py
```

Edit `config.yaml` to point at your inputs before the first run. See the repo README for the config fields.

### Next.js and TypeScript

Repos: `money-counter`, the `neuro` frontend (under `frontend/`).

Install [Node.js](https://nodejs.org) (an LTS release works). Then from the repo root:

```bash
npm install              # install dependencies
npm run dev              # start the dev server (money-counter serves at http://localhost:3000)
npm run lint             # run ESLint
npm run build            # production build
```

Run `npm run lint` and `npm run build` before you push so CI stays green. The `neuro` frontend
uses Vite and Vue, so run its commands from the `frontend/` directory; check its `package.json`
scripts, since the test and type-check commands differ there.

### Claude Code prompt repos

Repos: `overseer`, `saul-swissman`, `the-factory`.

These have no build step and no package to install. They are prompt and protocol repos that you
open in [Claude Code](https://www.anthropic.com/claude-code). To work on one:

1. Clone the repo and open the folder in Claude Code.
2. Read the repo README and the `CLAUDE.md` file. They describe how the tool is meant to run.
3. Each repo ships a `launch.sh` that prepares its state and points you at the protocol. Read it before you run it.

Since there is nothing to compile, the review is about the prompts, the protocol docs, and the
launch script. Test your change by running the flow in Claude Code and describing what you saw in the pull request.

## How pull requests are reviewed

A maintainer reads every pull request. We look for the same things across all stacks:

- It does one thing, and the title says what.
- It builds, and the existing checks pass.
- New behavior has a test where the stack supports it.
- Public behavior or commands that changed are reflected in the README.
- No unrelated reformatting that buries the real change.

We may ask for changes. Push more commits to the same branch and the pull request updates on its
own. Once the checks pass and a maintainer approves, we merge. Most repos squash on merge, so your
commits become one commit with the pull request title, which is why that title follows Conventional Commits.

Found a security issue? Do not open a public issue. Follow the `SECURITY.md` in the repo or in the
[org `.github`](https://github.com/lambdaf-org/.github).

## Licensing

By contributing, you agree your work is licensed under the same license as the repository you
contribute to. This guide is MIT licensed; see [LICENSE](./LICENSE).
