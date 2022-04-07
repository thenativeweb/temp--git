# git

- Version control system
  - From the idea similar to Subversion (SVN), CVS, Team Foundation Server (TFS), Visual Source Safe, …
  - However, it's conceptually different from all of these
- Linus Torvalds
  - Developed git for managing the Linux kernel

- Centralized version control system
  - Works only online, no offline support
  - It's slow
  - Single point of failure
  - …

- Distributed version control system
  - There is no server, more like a P2P network
  - Works great when offline, because all actions are first and foremost local
  - It's super fast
  - There is no single point of failure
  - …

## Git commands

```shell
# Configure user name, email, …
$ git config [--global] user.name "Golo Roden"
$ git config [--global] user.email "golo.roden@thenativeweb.io"
$ git config [--global] core.editor "notepad"

# Initialize a new repository
$ git init

# Get an overview of the repository and working directory status
$ git status

# Send something from the working directory to the staging area
$ git add <file>

# Send *all* the changes from the working directory and its subdirectories to the staging area
$ git add .

# If you want to select which lines to add (yes, no, quit, all from here, none from here, edit, help)
$ git add --patch <file>

# Move the information that a file was deleted to the staging area
$ git rm <file>

# Commit changes from the staging area to the repository
$ git commit

# Commit changes from the staging area to the repository and set a message
$ git commit -m "<message>"

# Fix the commit message of your last commit
$ git commit -m "<message>" --amend

# Show changes in a file in the working directory, unless it has already been staged
$ git diff <file>

# Show changes in a file in the working directory, if it has already been staged
$ git diff --cached <file>

# Show the history of what has happened
$ git log

# Remove an already staged file from the staging area without losing the changes to it.
$ git restore --staged <file>

# Discard any changes in the working directory
$ git restore <file>

# Show all branches
$ git branch

# Create a new branch, but do not switch to it
$ git branch <name>

# Create a new branch, and switch to it
$ git checkout -b <name>

# Switch a branch
$ git checkout <name>

# Delete a branch (which has already been merged)
$ git branch -d <name>

# Delete a branch (even if it has not yet been merged), losing data
$ git branch -D <name>

# Merging a branch (you have to be in the target branch)
$ git merge <sourcebranch>

# Reverting a commit
$ git revert <commitid>

# Show was has changed in a commit
$ git show <commitid>

# Show all tags
$ git tag

# Create a new tag
$ git tag <tag>

# Delete a tag
$ git tag -d <tag>

# Stash your changes
$ git stash

# List all stashes
$ git stash list

# Apply your stashed changes back to your working directory
$ git stash apply 0

# Remove a stash
$ git stash drop 0

# Apply and remove a stash in a single step
$ git stash pop 0

# Turning a stash into a branch
$ git stash branch <branchname> 0

# Cherry pick a commit from another branch
$ git cherry-pick <commitid>
```

### Dangerous commands

**ONLY USE THESE COMMANDS IF YOU ABSOLUTELY KNOW WHAT YOU ARE DOING, AND ASSUME THAT TYPICALLY YOU DON'T KNOW WHAT YOU ARE DOING, SO DON'T USE THEM**

```shell
# Undoing a commit, and keep changes
$ git reset --soft HEAD~1

# Undoing a commit, and discard changes
$ git reset --hard HEAD~1
```

## A graphical representation of the repository

### Submitting changes to the repository

```mermaid
flowchart LR
  WorkingDirectory --git add--> StagingArea
  StagingArea --git restore--> WorkingDirectory
  StagingArea --git commit--> Repository
```

### How a fast-forward merge works

Given that a `NewFeat` branch was created and diverged from `main`:

```mermaid
flowchart LR
  Root
  Root --> cb6fa27
  cb6fa27 --> ad6cea7
  ad6cea7 --> 0bfa6bf
  0bfa6bf --> 1b6aec1
  1b6aec1 --> 5dfa631
  Main --> 0bfa6bf
  NewFeat --> 5dfa631
  HEAD --> NewFeat
```

All we need to do is update the `main` reference:

```mermaid
flowchart LR
  Root
  Root --> cb6fa27
  cb6fa27 --> ad6cea7
  ad6cea7 --> 0bfa6bf
  0bfa6bf --> 1b6aec1
  1b6aec1 --> 5dfa631
  Main --> 5dfa631
  NewFeat --> 5dfa631
  HEAD --> Main
```

### How detached head works

Given that a `NewFeat` branch was created and diverged from `main`, but deleted later on without being merged:

```mermaid
flowchart LR
  Root
  Root --> cb6fa27
  cb6fa27 --> ad6cea7
  ad6cea7 --> 0bfa6bf
  0bfa6bf --> 1b6aec1
  1b6aec1 --> 5dfa631
  Main --> 0bfa6bf
  HEAD --> Main
```

If we checkout one of the commits of the deleted branch

```mermaid
flowchart LR
  Root
  Root --> cb6fa27
  cb6fa27 --> ad6cea7
  ad6cea7 --> 0bfa6bf
  0bfa6bf --> 1b6aec1
  1b6aec1 --> 5dfa631
  Main --> 0bfa6bf
  HEAD --> 5dfa631
```

### How a non fast-forward merge works

Given that a `NewFeat` and a `NewFeat2` branch were created and diverged from `main`, and `main` itself also changed:

```mermaid
flowchart LR
  Root
  Root --> cb6fa27
  cb6fa27 --> ad6cea7
  ad6cea7 --> 0bfa6bf

  0bfa6bf --> 143f67a
  Main --> 143f67a

  0bfa6bf --> 1b6aec1
  1b6aec1 --> 5dfa631
  ShowCommand --> 5dfa631

  0bfa6bf --> f563793
  RevertCommand --> f563793

  HEAD --> Main
```

### How cherry-picking works

```mermaid
flowchart LR
  Root
  Root --> cb6fa27
  cb6fa27 --> ad6cea7
  Main --> ad6cea7
  HEAD --> Main

  ad6cea7 --> cherry(0bfa6bf)
  cherry(0bfa6bf) --> 56d5f4d
  Other --> 56d5f4d

  style cherry fill:#f00
```

With cherry-picking this becomes:

```mermaid
flowchart LR
  Root
  Root --> cb6fa27
  cb6fa27 --> ad6cea7
  ad6cea7 --> cherry2(copy 0bfa6bf)
  Main --> cherry2(copy 0bfa6bf)
  HEAD --> Main

  ad6cea7 --> cherry(0bfa6bf)
  cherry(0bfa6bf) --> 56d5f4d
  Other --> 56d5f4d

  style cherry fill:#f00
  style cherry2 fill:#f00
```

### How rebasing works

```mermaid
flowchart LR
  Root
  Root --> cb6fa27
  cb6fa27 --> ad6cea7
  Main --> ad6cea7
  HEAD --> Main

  ad6cea7 --> coi(0bfa6bf)
  coi(0bfa6bf) --> 56d5f4d
  Other --> 56d5f4d

  style coi fill:#444
```

With rebasing this becomes:

```mermaid
flowchart LR
  Root
  Root --> cb6fa27
  cb6fa27 --> ad6cea7
  ad6cea7 --> coi(0bfa6bf)
  Main --> coi(0bfa6bf)
  HEAD --> Main

  ad6cea7 --> 56d5f4d
  Other --> 56d5f4d

  style coi fill:#444
```

## Git in a team

- First step: Clone a repository from an existing user
- To synchronize we have two options:
  - Either Alice pushes her new commits to Bob
  - Or Bob pulls Alice's new commits from her machine

Alice                 Bob

newproj/        ----> newproj/
  .git/        /        .git/
  hello.go*   /         hello.go
  mod.go     /          mod.go
            /
git-server /          git-server

- Protocols for Git over networks
  - `local`: For a server that serves a repository over a file share
  - `git`: Real network protocol, but no authentication and read-only
  - `ssh`: The de-facto standard for communicating with Git servers
  - `http`: The (other) de-facto standard for communicatin with Git servers

## Connecting a local repository to a remote repository

```shell
# Create an alias name `origin` for git@github.com:...
$ git remote add origin git@github.com:thenativeweb/temp--git.git

# Push your repository to the remote
$ git push -u origin main
```
