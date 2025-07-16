# dce
random sam coding essentials :)

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

