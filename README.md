# dce
random sam coding essentials :)

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

