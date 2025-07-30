# dce
random sam coding essentials :)

## Jul 29, 2025
### GitHub templates
Github templates are prefilled forms that automatically appear when someone opens and issue or PR in your repo. It's great because it allows for consistency, saves time, and better collaboration. For this, we will only be focusing on PR templates, but this works with github issues as well.

#### Setting up
```bash
# create template directory in your repo root
mkdir -p https://github.com/leesamuel423
cd https://github.com/leesamuel423

# for a single PR template
touch PULL_REQUEST_TEMPLATE.md
```

#### Basic template ideas
```md
# Pull Request Template (.github/PULL_REQUEST_TEMPLATE.md)
## Description
Brief description of what this PR does

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
- [ ] I have tested my changes locally
- [ ] All tests pass

## Screenshots (if applicable)
Add screenshots here

## Related Issues
Closes #(issue number)
```

```md
# Bug Report (.github/ISSUE_TEMPLATE/bug_report.md)
---
name: Bug report
about: Create a report to help us improve
title: '[BUG] '
labels: 'bug'
assignees: ''
---

**Describe the bug**
A clear description of what the bug is.

**To Reproduce**
Steps to reproduce:
1. Go to '...'
2. Click on '....'
3. See error

**Expected behavior**
What you expected to happen.

**Screenshots**
If applicable, add screenshots.

**Environment:**
- OS: [e.g. macOS]
- Browser: [e.g. chrome, safari]
- Version: [e.g. 22]
```

#### How this works
1. Create templates in `.github/` folder
2. Push to repo, and github automatically detects them
3. User opens issue/PR and see the template pre-filled
4. They fill it out with the info you need

> [!TIP]
> Remember: Templates are guides, not rules. Keep them helpful but not restrictive - you want to encourage contributions, not scare people away with paperwork!



## Jul 22, 2025
### git reset --soft/hard
`git reset` is a "time machine" for undoing commits.

#### Why git reset
- Great for fixing commit mistakes (wrong commit message, forgot to add a file, etc)
- Clean up messy commit history
- Undo commits
- Start fresh

#### Basic Commands
```bash
# SOFT RESET → Keeps your changes, just undos the commit
git reset --soft HEAD~1     # undo last commit, keep changes staged
git reset --soft HEAD~3     # undo last 3 commits, keep all changes staged

# HARD RESET → Nuclear option! Deletes commites and changes
git reset --hard HEAD~1     # undo last commit and delete all changes
git reset --hard HEAD~3     # undo last 3 commits and delete all changes

# Reset to specific commit
git reset --soft abc123     # Go back to commit abc123, keep changes
git reset --hard abc123     # Go back to commit abc123, DELETE everything after
```

#### Steps
1. `git log` to see commit history
2. Choose reset type (remember, soft keeps changes, hard removes)
3. Pick how far back you want to go
4. Execute the reset and git will move your branch pointer backwards

#### Tips!
- Before using `--hard`, use `git status` first to see what you might lose
- Create backup branches before risky resets
- To undo a hard reset, you can use `git reflog` to find lost commits (kept for 30 days)

#### Example
```bash
# scenario: you made 3 commits but want to combine them into 1

# see your commits
git log --oneline
# abc123 fix typo
# def456 add feature
# ghi789 initial attempt

# soft reset to undo the 3 commits but keep all the code
git reset --soft HEAD~3

# now all your changes are staged, ready for one clean commit
git commit -m "add complete feature with fixes"

# hard reset example
# only if you want to completely delete recent work
git reset --hard HEAD~1  # This CANNOT be easily undone!
```



## Jul 16, 2025
### Build process w/ Make
[Make](https://www.gnu.org/software/make/manual/make.html) is a build automation tool that runs commands based on rules defined in a `Makefile`.
- Only rebuilds what is changed
- Replace complex build steps w/ simple commands
- All build processes defined in one place
- Language agnostic
- Great for complex build pipelines w/ parallel tasks

#### Why Make
- Intelligent dep management → Make only rebuilds what's necessary by checking timestamps
- Can run independent tasks simultaneously
- Automatic smart variables
    - Example:
    ```make
    # $@ = target name
    # $< = first dependency
    # $^ = all dependencies
    # $* = stem (matched by %)

    build/%.min.js: src/%.js
        @echo "Minifying $<..."
        uglifyjs $< -o $@ -c -m
        @echo "Created $@ (size: $$(wc -c < $@) bytes)"
    ```
- Pattern rules and wildcards
    - Example:
    ```make
    # convert all .md files to .html
    %.html: %.md
        pandoc $< -o $@

    # process all images
    processed/%.jpg: raw/%.jpg
        convert $< -resize 800x800 $@
    ```
- Conditional executions (great for commands based on environment)
- Execute any shell commands with full power
- Incremental builds for compiled languages
- Language agnostic, and can orchestrate different build systems for multi-language projects
- Self documenting
- Complex dependency graphs

#### Installation
```bash
# macOS
brew install make
```

#### Example
Create file named `Makefile` (no extension) in project root
```make
# Super Simple Makefile Example
.PHONY: help install dev build test clean setup

install:
    @echo "Installing deps...."
    npm install
    @echo "Installed deps"

dev:
    @echo "Running dev..."
    npm run dev

build: clean
    @echo "Building..."
    npm run build
    @echo "Production build ready"

test:
    @echo "Running tests..."
    npm test

clean:
    @echo "Removing build artifacts..."
    rm -rf dist build node_modules/.cache
    @echo "Clean!"

setup: install build
    @echo "Project ready"

help:
	@echo "Available commands:"
	@echo "  make install - Install dependencies"
	@echo "  make dev     - Start development server"
	@echo "  make build   - Build for production"
	@echo "  make test    - Run tests"
	@echo "  make clean   - Remove build artifacts"
	@echo "  make setup   - Full project setup"
```


## Jul 15, 2025
### Run github actions locally
[Act](https://github.com/nektos/act) is a CLI tool that lets you run GitHub Actions on your local machine using Docker. No need to push commits to test if CICD pipeline is working.

#### Installation
```bash
# macOS
brew install act
```

#### Basic commands
```bash
# run all workflows
act

# run push event workflows
act push

# run specific workflow file
act -W https://github.com/leesamuel423/workflows/{test-file.yml}

# run specific job
act -j {whatever-job}

# list all actions w/o running
act -l
```

#### How it works
1. Reads `https://github.com/leesamuel423/workflows/*.yml` files
2. Spins up docker containers mimicking github runners
3. Execute workflow steps in the isolated env
4. Report results

> [!TIP]
> Act uses Docker, so make sure Docker is running before using Act!

