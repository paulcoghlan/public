# Git

See [https://jwiegley.github.io/git-from-the-bottom-up/]


## Windows Auth

`git remote set-url origin https://paulcoghlan:<GH_PAT>@github.com/paulcoghlan/hugo-theme-hello-friend.git`
## Common operations

New branch: `git checkout -b <branch-name>`

Undo last commit: `git reset --hard HEAD^`

Rebase or Squash last 4 commits: `git rebase -i HEAD~4`

Stage partial commits: `git add -p`

Sync with upstream fork, avoid merge commits:

```sh
git fetch upstream
git checkout master
git merge upstream/master --ff-only
git push origin
```

Sync with origin

```sh
git fetch origin
git checkout master
git merge origin/master --ff-only
```

Clean local modified files but untracked files are untouched:
```
git checkout <branch> -- <file-path>
git checkout  -- .
```
## Less common operations

Diff branch: `git diff master..the_local_branch`

Make local branch same as remote: `git reset --hard origin/<branch>`
Move local `main` to origin `main`: `git reset --hard origin/main`
Move branch to align with another branch: `git branch -f <branch>`
Move branch (e.g. need to not be on branch): `git branch -f <branch> <commit_id>`
Move HEAD to a branch tip: `git reset --hard <branch>`

Remove a local branch: `git branch -d the_local_branch`

Rename branches - rename locally, delete origin one, push new origin one:

```sh
git branch -m <old-name> <new-name>
git push origin :<old-name>
git push origin <new-name>
```

Move base of branch from one commit to another: `git rebase --onto newBase oldBase feature/branch`

Clean ignored files (probably excessive): `git clean -dfX`

Clean untracked directories & files: `git clean -dfx` / dry-run `git clean -ndfx`

Save where I am before I do complicated stuff: `git checkout -b backup`

Remove files from a commit:

```sh
git reset HEAD^
git add <all the files you want - excluding the one you dont want>
git commit -C HEAD@{1}
```

Undo git pull conflict:

```sh
git reflog show
git reset <chosen commit>
```

List remotes: `git remote -v`

Cherry-pick: `git cherry-pick <commit-sha>`

Force move tip of branch: `git branch -f <branch> <commit-sha>`

Remove old refs: `git fetch -a --prune` 

## Digging deeper

- working tree = what is on your SSD
- index/staging area = `git add/rm` commands "stages" files in the index (represented in `.git/`)
- repository = `git commit` command moves staged file changes into repository

`reset` - hard, soft or mixed (default), updates *index* moving branch to point that `HEAD` is pointing to

`git reset --hard 1.1` - file contents same as repo

`git reset 1.1` - resets the index but not the working tree and reports what has not been updated

`git reset --soft 1.1` - does not touch the index file or the working tree at all, leaves all your changed files "Changes to be committed", as git status would put it

`git checkout` - updates *working tree*, moving `HEAD` if new branch created (otherwise creates detached head)

`reflog` super-useful when you've made a mistake, logs all that you've done on a branch

`bisect` helps find last known good commit

`revert` creates anti-commit

`merge-base` most recent shared commit between 2 branches.  e.g. find point at which branch for A begins: `git merge-base A master`

Fetching a remote branch:

1. `6e8fab43..bea7535b  dev        -> origin/dev` - branch started at `6e8fab43`, ends at `bea7535b`
2. `git checkout -b how-it-was-before 6e8fab43` - create new branch starting from same point
3. Log/show branch:
    a. `git log 6e8fab43..bea7535b`
    b. `git show 6e8fab43..bea7535b`
    c. `git diff 6e8fab43..bea7535b`

### Reverting some directory in a branch

1. Diff folder in branch: `git diff main..<some_sha> -- afolder/`
2. Generate revert commit from patch: `| git apply -R`
3. Putting it together: `git diff main´´..bea7535b -- afolder/ | git apply -R`

## Search commit messages

`git show :/foozle` - search commit

## Reflog

`git-reflog` - list the SHAs of `HEAD` commits
`git-reflog some-branch` - list where `some-branch` has been

## `@` notation

- `@{17}` (HEAD as it was 17 ´actions ago)
- `some-branch@{'Aug 22'}` (some-branch as it was last August 22)
- `git show dev@{'Aug 22'}:path/to/some/file.txt` - Print out that file, as it was on dev, as dev was on August 22

`git reset --hard <SHA>`

## Aliases

```sh
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
```

## Misc

- `Detached HEAD` mode: `HEAD` normally points to branch, but in `detached head` mode it just points to a commit
- `<tree-ish>` = pointer to Git directory tree (not object!), e.g.: `master:foo`, `main~3`, `<sha1>`
- `pathspec` limits to certain files/directories `*.js`, `.`, ` */*.c`
- `tags` points to a specific tag object *or* commit object, lives in `refs/tags/`. Not updated by commits.
- `branches` points to a specific commit object, lives in `refs/heads/`.  Updated by a commit.
- `HEAD` must point to branch or a commit object

## Even Deeper

Given `echo 'Hello, world!' > greeting.txt`:

- *blob* = hash (see `git hash-object <file>`)
- *tree* = tree + blob (e.g. `git ls-tree <commit>`)
- *commit* = tree + parent + metadata

- `git hash-object greeting.txt` - return hash
- `git cat-file blob af5626b` - return blob contents
- `git ls-tree HEAD` -
- `git cat-file commit HEAD` -

## Commit messages

- 1st line is subject, without period
- Imperative mood
- Don't trigger Github (ie. `#1234` `@username`)

## Amend Workflow

- `git commit --amend -m "an updated commit message"`
- Show files in a given commit: `git show --format= --name-only HEAD`

## Signing Commits

```
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/<keyname>.pub
```


## Philosophy

Private vs Public history
