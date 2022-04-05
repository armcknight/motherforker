# `motherforker`

Automatically clone and/or fork every repo in the dependency tree of a project.

High-profile incidents in the open-source software supply-chain are not merely theoretical; they're a real risk, and happen with some regularity. Forking dependencies can mitigate [packages disappearing](https://news.ycombinator.com/item?id=16092132), [changing in undesirable ways](https://news.ycombinator.com/item?id=29863672), or being [taken over](https://news.ycombinator.com/item?id=29122098) and/or [injected with malware](https://news.ycombinator.com/item?id=30717382). Keeping packages on a server you control can prevent delays caused by [service outages in GitHub](https://news.ycombinator.com/item?id=30790593), [AWS](https://news.ycombinator.com/item?id=29473630), etc. If that server is colocated with the build machine on a LAN, build times may see improved performance as well.

However, manually forking every repo in a dependency tree that may contain thousands of packages is impossible. We need an automated solution.

## A note on terminology

A [fork](https://en.wikipedia.org/wiki/Fork_(software_development)) is really any clone that evolves independently of the remote repository from which it was cloned (not to be confused with a [process fork](https://en.wikipedia.org/wiki/Fork_(system_call))). The distributed nature of Git means that any fork can become a new remote that may be treated as an upstream source of truth by other developers, who can clone or fork it in turn. See [Git's description of the Integration-Manager Workflow](https://git-scm.com/book/en/v2/Distributed-Git-Distributed-Workflows#wfdiag_b). It all comes down to what you have set in your [git remotes](https://git-scm.com/docs/git-remote).

A GitHub "fork" is an automated process of cloning a repo, creating a new remote and then pushing the clone to that remote, which is then displayed with its own GitHub page with special decorations. GitHub retains metadata that helps certain workflows, like showing the original repo as a link, choosing which remote should receive PRs, or displaying the tree of all forks.

In this project, the former operation is called `clone` and the latter is called `fork`. To prevent populating your GitHub account with a large volume of ancillary repos, consider first using `clone` to simply get a copy of all of your dependencies' code on a local machine. Use `fork` if you want to share those forks amongst a development team; it's recommended to use a GitHub organization, or even create a GitHub user per project, to contain all the forks in isolation.

# Usage

## `clone`

Download a copy of each dependency's codebase locally.

## `fork`

Do everything `clone` does and additionally create a GitHub fork of each dependency's repo. You can specify a target hub to which all the forks should be pushed, e.g. if a project uses dependencies hosted in a mixture of hubs, such as GitHub, GitLab, and independent servers, they are all pushed to GitHub.

## `sync`

For a given project's dependency tree, update each clone/fork to the current version. (E.g. if `clone`/`fork` was previously run and the sole dependency was at 0.1.0 but in a subsequent run it was bumped to 0.2.0, then update the clone/fork to that revision by pulling from upstream.) Add and prune dependencies that are added or removed from the dependency tree between runs of the tool for a given project (by deleting the codebase locally and, if there's a remote version on GitHub from running `fork`, delete that repo)..

# TODO

- [ ] Implement `clone` for each package manager:
  - [ ] NPM (see [`npm-ls`](https://docs.npmjs.com/cli/v6/commands/npm-ls))
  - [ ] Rubygems
  - [ ] CocoaPods (see [`cocoapods-dependencies`](https://github.com/segiddins/cocoapods-dependencies))
  - [ ] SPM
  - [ ] Pip (see [`pipdeptree`](https://pypi.org/project/pipdeptree/))
  - [ ] Homebrew
  - [ ] Nix
  - [ ] ASDF
  - [ ] ...others?
- [ ] Implement `fork` for:
  - [ ] GitHub
  - [ ] GitLab
  - [ ] AWS CodeCommit
  - [ ] ...others?
- [ ] Don't clone/fork repos that have already been forked. Reuse previous forks commonly used between two projects.
  - [ ] If local (by having run `clone`, this is just a test if it already exists locally.
  - [ ] If remote (by having run `fork`, test if the supplied user credential owns the forked repo remotely).
- [ ] Make `fork` work for private repos by pushing a private fork instead of using the standard GitHub fork API.
- [ ] Currently gets the repo name from the first fetch remote; robustly handle the case when there's more than one, interactively and with an option argument
