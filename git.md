# Git

See [https://jwiegley.github.io/git-from-the-bottom-up/]

## Common operations

New branch: `git checkout -b <branch-name>`

Undo last commit: `git reset --hard HEAD^`

Rebase or Squash last 4 commits: `git rebase -i HEAD~4`

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

## Less common operations

Diff branch: `git diff master..the_local_branch`

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

Move tag (e.g. need to not be on branch): `git branch -f <tag> <commit_id>`

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

## Digging deeper

- working tree = what is on your SSD
- index/staging area = `git add/rm` commands "stages" files in the index (represented in `.git/`)
- repository = `git commit` command moves staged file changes into repository

`reset` - hard, soft or mixed (default)

`git reset --hard 1.1` - file contents same as repo

`git reset 1.1` - resets the index but not the working tree and reports what has not been updated

`git reset --soft 1.1` - does not touch the index file or the working tree at all, leaves all your changed files "Changes to be committed", as git status would put it

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

1. Diff folder in branch: `git diff 6e8fab43..bea7535b -- afolder/`
2. Generate revert commit from patch: `| git apply -R`
3. Putting it together: `git diff 6e8fab43..bea7535b -- afolder/ | git apply -R`

## Search commit messages

`git show :/foozle` - search commit

## Reflog

`git-reflog` - list the SHAs of `HEAD` commits
`git-reflog some-branch` - list where `some-branch` has been

## `@` notation

- `@{17}` (HEAD as it was 17 actions ago)
- `some-branch@{'Aug 22'}` (some-branch as it was last August 22)
- `git show dev@{'Aug 22'}:path/to/some/file.txt` - Print out that file, as it was on dev, as dev was on August 22

`git reset --hard <SHA>`

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

## Philosophy

Private vs Public history
