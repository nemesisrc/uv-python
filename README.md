# Complete `uv` Python Package Manager Tutorial: Beginner to Advanced

`uv` is a fast Python package and project manager written in Rust. It can replace several traditional Python tools, including `pip`, `pip-tools`, `virtualenv`, and parts of `pyenv` and `Poetry` workflows.

This guide covers the practical command reference, from installing Python and creating virtual environments to dependency locking, publishing packages, workspaces, and CI/CD.

Since you work with Python frameworks such as FastAPI, Flask, and Django, I'll include examples for real backend projects as well.

## 1. Understand `uv` first

Package management

Install, upgrade, remove and inspect Python packages.

`uv add`, `uv remove`, `uv pip install`

Project management

Create projects, manage dependencies and generate lockfiles.

`uv init`, `uv sync`, `uv lock`

Python and tool management

Install Python versions and run tools without manually installing them globally.

`uv python install`, `uv run`, `uvx`

### `uv` vs traditional Python commands

| Traditional command/tool           | `uv` equivalent             |
| ---------------------------------- | --------------------------- |
| `pip install requests`             | `uv pip install requests`   |
| `python -m venv .venv`             | `uv venv`                   |
| `pip freeze`                       | `uv pip freeze`             |
| `pip uninstall requests`           | `uv pip uninstall requests` |
| `pip-tools` compile workflow       | `uv pip compile`            |
| Install a Python version manually  | `uv python install 3.12`    |
| Run a one-off CLI tool with `pipx` | `uvx ruff check .`          |
| Create a managed Python project    | `uv init`                   |
| Install project dependencies       | `uv sync`                   |

Important distinction: `uv add` and `uv sync` belong to the project-management workflow. `uv pip install` is the pip-compatible workflow. They are related, but they are not interchangeable in every situation.

## 2. Install `uv`

### Windows

Open PowerShell and run:

```
# Install uv
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"

# Verify installation
uv --version

# Display help
uv --help
```

Alternatively, if you have Python and pip installed:

```
pip install uv
```

### macOS and Linux

```
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Alternatively:

```
pip install uv
```

Official installation guide: [Install uv](https://docs.astral.sh/uv/getting-started/installation/?utm_source=chatgpt.com).

### Essential verification commands

```
uv --version
uv --help
uv help
uv help init
uv help add
uv help run
uv help pip
uv help python
```

Use `uv help <command>` whenever you need the full options for a command. The official CLI reference documents the command families and their available flags.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://docs.astral.sh\&sz=32)

uv



## 3. Install and manage Python versions

You do not need to install every Python version manually from python.org. `uv` can manage Python installations for you.

### Python version commands

| Command                           | Purpose                                           |
| --------------------------------- | ------------------------------------------------- |
| `uv python install 3.12`          | Install Python 3.12                               |
| `uv python install 3.13`          | Install Python 3.13                               |
| `uv python install 3.12 3.13`     | Install multiple versions                         |
| `uv python list`                  | List available Python versions                    |
| `uv python list --only-installed` | Show installed versions                           |
| `uv python find`                  | Find a suitable installed interpreter             |
| `uv python find 3.12`             | Find Python 3.12                                  |
| `uv python dir`                   | Display the managed Python installation directory |
| `uv python uninstall 3.12`        | Uninstall a managed Python version                |
| `uv python pin 3.12`              | Pin the current project to Python 3.12            |

Examples:

```
# Install Python 3.12
uv python install 3.12

# Install Python 3.13
uv python install 3.13

# See installed versions
uv python list --only-installed

# Find the interpreter for Python 3.12
uv python find 3.12

# Pin a project to Python 3.12
uv python pin 3.12
```

`uv python pin` generally creates or updates a `.python-version` file in the project directory.

For example:

```
my_project/
├── .python-version
├── pyproject.toml
└── main.py
```

The `.python-version` file contains:

```
3.12
```

This tells `uv` which Python version to prefer when working in that project. It does not force every external Python launcher on your computer to use that version.

Important: `uv python uninstall` removes a Python installation managed by `uv`; it is not intended to uninstall a separate system Python installation.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://github.com\&sz=32)

GitHub

+1



## 4. Create virtual environments

A virtual environment isolates a project's Python packages from other projects.

### Create a default environment

```
uv venv
```

By default, this creates `.venv` in the current directory.

### Specify a Python version

```
uv venv --python 3.12
uv venv --python 3.13
```

### Specify a directory

```
uv venv .venv
uv venv myenv
uv venv C:\python-envs\fastapi-env
```

### Specify the environment name and interpreter

```
uv venv --python 3.12 .venv
```

### Activate the environment

In Windows PowerShell:

```
.\.venv\Scripts\Activate.ps1
```

In Windows Command Prompt:

```
.venv\Scripts\activate.bat
```

In Git Bash, macOS or Linux:

```
source .venv/bin/activate
```

Deactivate it:

```
deactivate
```

You can also avoid activation entirely:

```
uv run --python 3.12 python --version
```

### Install a package in the environment

```
uv pip install requests
```

### Check which Python is being used

```
python --version
python -c "import sys; print(sys.executable)"
```

Best practice: For new projects managed by `uv`, use `uv init` and `uv run` instead of manually creating and activating environments whenever possible.

## 5. Initialize projects with `uv init`

`uv init` creates the initial files for a project.

### Basic initialization

```
# Initialize in the current directory
uv init

# Create a project in a new directory
uv init my_project

# Initialize with a particular Python version
uv init --python 3.12 my_project
```

### Different project types

```
# Application project
uv init --app my_app

# Installable Python package project
uv init --package my_package

# Library project
uv init --lib my_library

# Minimal project setup
uv init --bare my_project

# Single-file script project
uv init --script hello.py
```

These modes serve different purposes:

- `--app`: an application-oriented project.
- `--package`: a project intended to be built and installed as a package.
- `--lib`: a library-oriented project.
- `--bare`: a minimal project initialization.
- `--script`: a standalone Python script with inline dependency metadata.

Exact generated files can vary with the project type and `uv` version.

### Example: Create a FastAPI backend

```
uv init --app background-remover
cd background-remover
uv add fastapi "uvicorn[standard]" python-multipart
```

Add image-processing dependencies later:

```
uv add pillow rembg
```

For this example, `python-multipart` supports multipart form uploads. The background-removal model and its runtime dependencies may require additional system setup, depending on your deployment environment.

### Typical project structure

```
background-remover/
├── .python-version
├── .venv/
├── pyproject.toml
├── uv.lock
└── main.py
```

The virtual environment and lockfile may be created lazily when you first run a command that needs them.

## 6. Manage dependencies using `uv add` and `uv remove`

This is the primary dependency-management workflow for `uv` projects.

### Add packages

```
uv add requests
uv add fastapi
uv add "uvicorn[standard]"
uv add sqlalchemy
uv add "psycopg[binary]"
```

Add multiple packages in one command:

```
uv add fastapi uvicorn sqlalchemy alembic
```

When you use `uv add`, the package is declared in `pyproject.toml`, and `uv` updates the lockfile and project environment as needed.

### Add development dependencies

```
uv add --dev pytest
uv add --dev ruff
uv add --dev mypy
```

You can add several development tools together:

```
uv add --dev pytest ruff mypy
```

Development dependencies are for testing, linting, and other development tasks rather than your application's normal runtime requirements.

### Add optional dependencies

```
uv add --optional docs mkdocs
```

This adds a dependency to an optional dependency group named `docs`, which can be installed using the corresponding extra when configured.

### Install a specific version

```
uv add "requests==2.32.4"
```

This requests an exact version in your project's dependency declaration.

### Specify a compatible version range

```
uv add "fastapi>=0.115,<1.0"
```

Other common version specifiers:

```
uv add "requests>=2.30"
uv add "requests~=2.32"
uv add "requests!=2.32.0"
```

These mean, respectively, a minimum version, a compatible release range, and an exclusion.

### Remove dependencies

```
uv remove requests
uv remove fastapi sqlalchemy
```

Remove a development dependency:

```
uv remove --dev pytest
```

Remove an optional dependency:

```
uv remove --optional docs mkdocs
```

`uv remove` removes project-declared dependencies. It does not automatically remove a package that you separately installed using `uv pip install` but never declared in the project.

### Add dependencies from an existing requirements file

```
uv add -r requirements.txt
```

This is useful when migrating an existing project from pip. Review the resulting `pyproject.toml` and resolve any conflicting or platform-specific requirements.

## 7. Run Python programs using `uv run`

`uv run` executes commands in the project's environment. It can ensure the environment is synchronized before execution.

### Run a Python file

```
uv run python main.py
uv run python app.py
uv run python manage.py runserver
```

### Run a module

```
uv run python -m pytest
uv run python -m pip --version
uv run python -m http.server 8000
```

### Run a FastAPI application

```
uv run uvicorn main:app --reload
```

### Run with a specific Python version

```
uv run --python 3.12 python main.py
```

### Run with a temporary dependency

```
uv run --with requests python script.py
uv run --with rich python script.py
```

This makes a dependency available for that invocation without adding it to the project's permanent dependency declarations.

You can specify a version:

```
uv run --with "requests==2.32.4" python script.py
```

### Run a command without synchronizing

```
uv run --no-sync python main.py
```

Use this when you deliberately want to skip automatic environment synchronization. The environment must already contain everything the program needs.

### Run in offline mode

```
uv run --offline python main.py
```

This prevents network access by `uv` for the operation. It works only when the required metadata and packages are available locally.

### Useful execution flags

| Command                                 | Meaning                                       |
| --------------------------------------- | --------------------------------------------- |
| `uv run --locked python main.py`        | Require the lockfile to remain up to date     |
| `uv run --frozen python main.py`        | Use the existing lockfile without updating it |
| `uv run --no-sync python main.py`       | Skip environment synchronization              |
| `uv run --with requests python main.py` | Make a temporary dependency available         |
| `uv run --python 3.12 python main.py`   | Select Python 3.12                            |

Use `uv run --help` to check all supported options for your installed version.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://docs.astral.sh\&sz=32)

uv



## 8. Understand `pyproject.toml` and `uv.lock`

These are two of the most important files in a `uv` project.

### `pyproject.toml`

Stores project metadata and declared dependencies.

A simplified example:

```
[project]
name = "background-remover"
version = "0.1.0"
requires-python = ">=3.12"
dependencies = [
    "fastapi",
    "uvicorn[standard]",
    "python-multipart",
]

[dependency-groups]
dev = [
    "pytest",
    "ruff",
]
```

You generally modify dependencies with `uv add` and `uv remove`, rather than editing the list manually.

### `uv.lock`

Stores resolved dependency versions and other information needed to reproduce the environment.

You normally commit both files to Git:

```
pyproject.toml
uv.lock
```

You normally do not commit `.venv/`.

### Lockfile commands

| Command                              | Purpose                                                     |
| ------------------------------------ | ----------------------------------------------------------- |
| `uv lock`                            | Resolve dependencies and update the lockfile when necessary |
| `uv lock --check`                    | Check whether the lockfile is up to date                    |
| `uv lock --upgrade`                  | Upgrade all eligible locked dependencies                    |
| `uv lock --upgrade-package requests` | Upgrade a specific package and its relevant dependencies    |
| `uv sync`                            | Synchronize the project environment                         |
| `uv sync --locked`                   | Require the lockfile to be current                          |
| `uv sync --frozen`                   | Synchronize using the existing lockfile without updating it |
| `uv sync --no-dev`                   | Exclude development dependencies                            |
| `uv sync --all-groups`               | Include all dependency groups                               |
| `uv sync --extra docs`               | Include a named optional extra                              |
| `uv sync --all-extras`               | Include all optional extras                                 |
| `uv tree`                            | Display the project dependency tree                         |

Examples:

```
# Generate or update the lockfile
uv lock

# Upgrade all eligible dependencies
uv lock --upgrade

# Upgrade only requests
uv lock --upgrade-package requests

# Verify that the lockfile is current
uv lock --check

# Install dependencies into the project environment
uv sync

# Synchronize without development dependencies
uv sync --no-dev

# Include all optional extras
uv sync --all-extras
```

Important difference:

- `uv lock --upgrade` resolves eligible dependencies to newer versions within the constraints you have declared.
- `uv sync` installs the project's resolved dependencies and removes extraneous packages from the project environment by default.
- `uv sync --locked` is useful in CI/CD because it fails if the lockfile needs updating.
- `uv sync --frozen` does not check whether the lockfile reflects the current project metadata. Use it only when that is intentional.

For production deployments, commit `uv.lock` and use a lockfile-enforcing workflow.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://github.com\&sz=32)

GitHub

+1



## 9. Inspect installed packages and dependency trees

These commands help you debug package versions and dependency conflicts.

### Project dependency tree

```
uv tree
```

Show why a package is installed:

```
uv tree --invert requests
```

Filter the tree to a particular dependency:

```
uv tree --package fastapi
```

These options are useful for understanding indirect dependencies. Check `uv tree --help` for the precise options supported by your installed release.

### Inspect the project environment

```
uv pip list
uv pip freeze
uv pip show fastapi
uv pip check
uv pip tree
```

Example output from `uv pip freeze` might look like:

```
fastapi==0.115.0
pydantic==2.9.2
starlette==0.38.6
```

These are illustrative versions, not a recommended current dependency set.

### Find dependency problems

```
uv pip check
```

This checks whether installed packages have compatible declared dependencies.

### Show package information

```
uv pip show requests
uv pip show fastapi sqlalchemy
```

### Search installed packages

```
uv pip list
uv pip list --outdated
```

The exact availability of options such as `--outdated` depends on the command and version. Consult `uv pip list --help` before relying on a pip-compatible flag.

## 10. The complete `uv pip` workflow

The `uv pip` interface is especially useful when you are working with an existing project that already uses `requirements.txt` or a manually created virtual environment.

### A. Install packages

```
uv pip install requests
uv pip install fastapi uvicorn
uv pip install "uvicorn[standard]"
```

Install from a requirements file:

```
uv pip install -r requirements.txt
```

Install a specific version:

```
uv pip install "requests==2.32.4"
```

Upgrade a package:

```
uv pip install --upgrade requests
```

Install into a particular virtual environment:

```
uv pip install --python .venv\Scripts\python.exe requests
```

On macOS or Linux:

```
uv pip install --python .venv/bin/python requests
```

### B. Uninstall packages

```
uv pip uninstall requests
uv pip uninstall fastapi sqlalchemy
```

### C. List installed packages

```
uv pip list
uv pip freeze
```

Save installed versions to a requirements file:

```
uv pip freeze > requirements.txt
```

Remember that freezing an environment captures its installed packages. It is not the same as maintaining a clean, intentionally curated dependency declaration.

### D. Show package details

```
uv pip show requests
```

### E. Check dependencies

```
uv pip check
```

### F. Compile dependencies into a locked requirements file

Create a file named `requirements.in`:

```
fastapi
uvicorn[standard]
requests
```

Then run:

```
uv pip compile requirements.in -o requirements.txt
```

The output includes resolved dependencies and versions.

You can also compile a project declaration:

```
uv pip compile pyproject.toml -o requirements.txt
```

### G. Synchronize from the compiled requirements file

```
uv pip sync requirements.txt
```

Unlike `uv pip install`, `uv pip sync` removes packages that are not specified in the input file. This makes it suitable for reproducing a known environment.

### H. Compile with constraints

Create `constraints.txt`:

```
urllib3<3
```

Then:

```
uv pip compile requirements.in --constraint constraints.txt -o requirements.txt
```

A constraints file restricts acceptable versions without necessarily adding the constrained package as a direct dependency.

### I. Compile with a different Python target

```
uv pip compile requirements.in --python-version 3.12 -o requirements.txt
```

For platform-dependent packages, the target platform and Python version can affect the resulting resolution. Review the help for `uv pip compile` for all supported target and resolution options.

Official reference: [uv pip documentation](https://docs.astral.sh/uv/pip/?utm_source=chatgpt.com).&#x20;

[image](https://www.google.com/s2/favicons?domain=https://docs.astral.sh\&sz=32)

uv



## 11. Install and run developer tools

You do not need to install every developer tool into every project.

### A. Run a tool temporarily with `uvx`

`uvx` is an alias for `uv tool run`.

```
# Lint Python files
uvx ruff check .

# Format Python files
uvx ruff format .

# Run the test suite
uvx pytest

# Run a type checker
uvx mypy .

# Run a code-quality tool
uvx pre-commit --version
```

`uvx` runs the tool in an isolated environment, downloading it if necessary.

### B. Install a tool for repeated use

```
uv tool install ruff
uv tool install pytest
uv tool install pre-commit
```

### C. List installed tools

```
uv tool list
```

### D. Upgrade tools

```
uv tool upgrade ruff
uv tool upgrade --all
```

### E. Uninstall tools

```
uv tool uninstall ruff
```

### F. Tool directories and shell configuration

```
uv tool dir
uv tool update-shell
```

The first shows the tool installation directory. The second updates the shell configuration so installed tool executables can be found where supported.

### G. Run a different package under a command name

```
uv tool run --from httpie http httpbin.org/get
```

Here, the Python distribution is `httpie`, while its executable is `http`.

For a repeatable project workflow, prefer declaring tools such as Ruff and pytest as development dependencies with `uv add --dev`. Use `uvx` for convenient one-off execution.

## 12. Run standalone Python scripts with dependencies

You do not need a complete project directory to use `uv`.

Create `hello.py`:

```
print("Hello from Python")
```

Run it:

```
uv run hello.py
```

### Add a dependency temporarily

```
uv run --with requests hello.py
```

### Create a self-contained script with inline metadata

```
uv init --script scraper.py
uv add --script scraper.py requests
```

The script can contain inline metadata managed by `uv`, so its dependencies do not need to be declared in a separate project.

Run it with:

```
uv run scraper.py
```

### Remove a script dependency

```
uv remove --script scraper.py requests
```

### Use a different Python version

```
uv run --python 3.12 scraper.py
```

This workflow is particularly useful for automation scripts, quick API tests, data processing, and small utilities.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://github.com\&sz=32)

GitHub

+1



## 13. Advanced dependency management

### A. Dependency groups

For a project that needs separate testing and documentation tools:

```
uv add --dev pytest ruff
uv add --group docs mkdocs
```

Run a command with a particular group installed:

```
uv run --group docs mkdocs serve
```

Include all dependency groups:

```
uv sync --all-groups
```

Exclude development dependencies:

```
uv sync --no-dev
```

### B. Optional extras

Optional extras are useful when users need to install additional functionality.

For example, a library could expose an extra called `postgres`:

```
[project.optional-dependencies]
postgres = ["psycopg[binary]"]
```

Install the extra in a project using:

```
uv sync --extra postgres
```

The project must define the extra first.

### C. Install a Git dependency

```
uv add "git+https://github.com/psf/requests.git"
```

Pin to a branch:

```
uv add "git+https://github.com/psf/requests.git@main"
```

Pin to a commit for reproducibility:

```
uv add "requests @ git+https://github.com/psf/requests.git@<commit-sha>"
```

Replace `<commit-sha>` with a real commit hash. Avoid relying on a moving branch for production dependencies.

### D. Install a local package

```
uv add ../shared-library
```

For a local editable dependency:

```
uv add --editable ../shared-library
```

Editable installations are useful when you want changes to the local library's source code to be reflected without reinstalling the package each time.

### E. Use a package index or mirror

```
uv pip install --index-url https://pypi.org/simple requests
```

You can configure index behavior in project configuration or environment variables for corporate mirrors. Use trusted indexes and avoid embedding private credentials directly in command history or source files.

### F. Use constraints

```
uv pip install -c constraints.txt -r requirements.txt
```

Constraints control acceptable versions. They do not replace your project's main dependency declarations.

### G. Reinstall packages

```
uv pip install --reinstall requests
```

Force a reinstall of all packages listed in a requirements file:

```
uv pip install --reinstall -r requirements.txt
```

Use reinstall commands when you suspect a damaged installation or need to replace package files. They are not a substitute for resolving dependency conflicts.

## 14. Workspaces and monorepos

A workspace lets you manage multiple related Python packages from a shared repository.

Example structure:

```
company-platform/
├── pyproject.toml
├── uv.lock
└── packages/
    ├── api/
    │   └── pyproject.toml
    └── shared/
        └── pyproject.toml
```

Initialize the root project and add workspace members according to the workspace configuration.

A simplified root configuration:

```
[tool.uv.workspace]
members = ["packages/api", "packages/shared"]
```

Inspect the workspace:

```
uv workspace --help
uv tree
uv sync
```

Add a local workspace dependency from a member project:

```
uv add shared --package api
```

The exact package names must match the projects' declared names. Workspace configuration and dependency relationships should be set up consistently so the shared lockfile resolves the members correctly.

Workspaces are useful when a backend, shared library, and internal tooling are developed together. See the [official workspace guide](https://docs.astral.sh/uv/concepts/projects/workspaces/?utm_source=chatgpt.com).

## 15. Build, version and publish Python packages

These commands are mainly relevant when you create a reusable Python library rather than just an application.

### A. Build distributions

```
uv build
```

This normally creates a source distribution (`.tar.gz`) and a wheel (`.whl`) under `dist/`.

### B. Build a specific project

```
uv build --package my_library
```

This is useful when building a particular package within a workspace.

### C. Manage the version

```
uv version
uv version 1.0.0
uv version --bump patch
uv version --bump minor
uv version --bump major
```

Version-bump commands depend on the project's version configuration and the installed `uv` version.

### D. Publish a package

```
uv publish
```

For TestPyPI:

```
uv publish --publish-url https://test.pypi.org/legacy/
```

For publishing, configure credentials using a supported secure mechanism, such as an environment variable or trusted publishing. Do not put API tokens directly into source code or commit them to Git.

Useful official references:

- [Packaging Python projects](https://docs.astral.sh/uv/guides/package/?utm_source=chatgpt.com)
- [Publishing packages](https://docs.astral.sh/uv/guides/publish/?utm_source=chatgpt.com)

### E. Export dependencies

```
uv export --format requirements.txt --output-file requirements.txt
```

This is useful if a deployment platform or Docker workflow requires a traditional requirements file.

The export reflects the project's dependency resolution and selected export options; it is not a replacement for maintaining `uv.lock`.

## 16. Cache management and maintenance

`uv` caches downloaded packages and metadata to speed up future installations.

| Command                   | Purpose                                      |
| ------------------------- | -------------------------------------------- |
| `uv cache dir`            | Show the cache directory                     |
| `uv cache clean`          | Clear the cache                              |
| `uv cache clean requests` | Clear cached entries for a named package     |
| `uv cache prune`          | Remove unused cache entries                  |
| `uv python dir`           | Show the managed Python directory            |
| `uv tool dir`             | Show the tool installation directory         |
| `uv self version`         | Display the installed `uv` version           |
| `uv self update`          | Update `uv` where self-updating is supported |

Examples:

```
uv cache dir
uv cache clean
uv cache clean requests
uv cache prune
uv self version
uv self update
```

Caution: `uv cache clean` can remove cached downloads that would otherwise be reused. It generally does not uninstall your project dependencies, but subsequent installations may need to download them again. `uv cache prune` is the more targeted maintenance operation.

If `uv` was installed through a package manager, that manager may be the appropriate way to update it.

## 17. Authentication and private package indexes

For private repositories or package registries, the CLI includes authentication commands.

```
uv auth --help
uv auth login --help
uv auth logout --help
uv auth token --help
uv auth dir
```

These commands manage authentication for supported services. The accepted service names and login flows depend on the service and installed `uv` release.

For private indexes, configure the appropriate index URL and credentials using the supported authentication mechanism. Avoid putting passwords or tokens in committed `pyproject.toml` files.

See the [official authentication command reference](https://docs.astral.sh/uv/reference/cli/?utm_source=chatgpt.com#uv-auth).

## 18. Environment variables and global options

Environment variables let you configure `uv` for your computer, CI/CD pipeline, or corporate environment.

### Useful environment variables

| Variable                 | Purpose                                       |
| ------------------------ | --------------------------------------------- |
| `UV_CACHE_DIR`           | Override the cache directory                  |
| `UV_PYTHON_INSTALL_DIR`  | Set the managed Python installation directory |
| `UV_TOOL_DIR`            | Set the tool installation directory           |
| `UV_PROJECT_ENVIRONMENT` | Override the project's environment path       |
| `UV_INDEX_URL`           | Configure the default package index URL       |
| `UV_OFFLINE`             | Enable offline mode                           |
| `UV_NO_CACHE`            | Disable cache usage for supported operations  |
| `UV_NATIVE_TLS`          | Use native system TLS certificates            |
| `UV_PYTHON_PREFERENCE`   | Influence Python interpreter selection        |

Example in PowerShell:

```
$env:UV_OFFLINE = "true"
uv sync
```

Reset it for the current PowerShell session:

```
Remove-Item Env:UV_OFFLINE
```

Example in Linux/macOS:

```
export UV_OFFLINE=true
uv sync
```

Unset it:

```
unset UV_OFFLINE
```

Environment variable names and supported values can change between releases. Refer to the [official environment variable reference](https://docs.astral.sh/uv/reference/environment/?utm_source=chatgpt.com) before configuring production systems.

### Global flags

These are commonly useful across different commands:

| Flag                 | Typical use                                    |
| -------------------- | ---------------------------------------------- |
| `--help`             | Display command help                           |
| `--version`          | Display version information where supported    |
| `-v` / `--verbose`   | Increase diagnostic output                     |
| `-q` / `--quiet`     | Reduce output                                  |
| `--offline`          | Avoid network access                           |
| `--no-cache`         | Avoid using the cache                          |
| `--directory PATH`   | Run from a specified directory where supported |
| `--project PATH`     | Select a project where supported               |
| `--config-file PATH` | Specify a configuration file where supported   |

Example:

```
uv -v sync
uv --offline sync
uv --no-cache pip install requests
uv --help
```

Do not assume every global flag works with every subcommand. Check the help for the specific command.

## 19. Newer project quality and security commands

Recent versions of `uv` include commands beyond package installation and environment management. Availability depends on your installed release.

### Format Python code

```
uv format
uv format --check
uv format --diff
```

This provides a convenient project-level formatting workflow.

### Run project checks

```
uv check
uv check --help
```

The behavior and supported options are version-dependent. Check your installed version's help rather than assuming it is a generic test runner.

### Audit dependencies

```
uv audit
uv audit --help
```

Use the installed CLI reference to verify the supported audit functionality and options for your release.

For the full current command list, see the [official uv CLI reference](https://docs.astral.sh/uv/reference/cli/?utm_source=chatgpt.com).&#x20;

[image](https://www.google.com/s2/favicons?domain=https://docs.astral.sh\&sz=32)

uv



## 20. Common mistakes and their solutions

Mistake 1: Using `uv add` outside a project

```
uv add requests
```

If no project is found, this fails. For a standalone environment, use `uv pip install requests`, or initialize a project first with `uv init`.

Mistake 2: Confusing `uv sync` with `uv pip install`

`uv sync` manages the project's declared dependencies and removes extraneous packages by default. `uv pip install` manually installs packages into an environment.

Mistake 3: Running the wrong Python interpreter

```
python --version
uv run python --version
```

These commands may report different versions if your shell and project use different interpreters.

Mistake 4: Installing a package but not declaring it

If you run `uv pip install requests` inside a project, that does not automatically add `requests` to `pyproject.toml`. Use `uv add requests` when it should become a project dependency.

Mistake 5: Deleting `uv.lock` unnecessarily

The lockfile is valuable for reproducible installations. Keep it under version control and regenerate it only when needed.

Mistake 6: Assuming `uvx` installs a tool permanently

`uvx ruff check .` runs Ruff in an isolated tool environment. For a persistent user-level installation, use `uv tool install ruff`.

## 21. Real-world workflows you should memorize

### Workflow A: Start a new FastAPI project

```
uv init --app my-api
cd my-api

uv add fastapi "uvicorn[standard]"
uv add --dev pytest ruff

uv run uvicorn main:app --reload
```

### Workflow B: Clone and run an existing `uv` project

```
git clone <repository-url>
cd <repository-folder>

uv sync --locked
uv run python main.py
```

Replace the placeholders with the actual repository URL and folder name. For a FastAPI project, the final command may instead be `uv run uvicorn main:app`.

### Workflow C: Run tests and format code

```
uv run pytest
uv run ruff check .
uv run ruff format .
```

If the tools are not project dependencies, add them first or invoke them using `uvx`.

### Workflow D: Upgrade dependencies

```
uv lock --upgrade
uv sync
uv tree
uv run pytest
```

Review the lockfile diff and test results before committing the updated dependencies.

### Workflow E: Prepare a production environment

```
uv lock --check
uv sync --locked --no-dev
uv run --locked python main.py
```

This verifies that the lockfile is current, installs the production dependency set, and runs the application without permitting an implicit lockfile update.

### Workflow F: Work with traditional requirements files

```
uv venv --python 3.12
uv pip install -r requirements.txt
uv pip check
uv pip freeze > requirements.txt
```

This workflow is useful for older repositories that do not use `pyproject.toml`.

## 22. Final command checklist

Use this as a compact revision sheet.

## Interactive command cheat sheet

Installation and helpPython managementVirtual environmentsProjects and dependenciesExecution and scriptsPip-compatible managementBuild and maintenance

### Projects and dependencies

Create project

uv init myproject

&#x20;Copy

Add dependency

uv add fastapi

&#x20;Copy

Add development dependency

uv add --dev pytest

&#x20;Copy

Remove dependency

uv remove fastapi

&#x20;Copy

Synchronize environment

uv sync

&#x20;Copy

Resolve lockfile

uv lock

&#x20;Copy

View dependencies

uv tree

&#x20;Copy

## 23. Official documentation for complete coverage

No short tutorial can enumerate every option, environment variable, authentication flow, and platform-specific behavior for every version of `uv`. The references below are the authoritative way to verify the complete command surface for your installed version.

[image](https://www.google.com/s2/favicons?domain=https://docs.astral.sh\&sz=32)

[Complete CLI reference](https://docs.astral.sh/uv/reference/cli/?utm_source=chatgpt.com)

All top-level commands, subcommands and supported flags.

[image](https://www.google.com/s2/favicons?domain=https://docs.astral.sh\&sz=32)

[Projects and dependencies](https://docs.astral.sh/uv/guides/projects/?utm_source=chatgpt.com)

Project setup, dependencies, lockfiles and environments.

[image](https://www.google.com/s2/favicons?domain=https://docs.astral.sh\&sz=32)

[Running scripts](https://docs.astral.sh/uv/guides/scripts/?utm_source=chatgpt.com)

Standalone scripts and inline dependencies.

[image](https://www.google.com/s2/favicons?domain=https://docs.astral.sh\&sz=32)

[Pip-compatible interface](https://docs.astral.sh/uv/pip/?utm_source=chatgpt.com)

Installation, compilation, synchronization and package inspection.

[image](https://www.google.com/s2/favicons?domain=https://docs.astral.sh\&sz=32)

[Environment variables](https://docs.astral.sh/uv/reference/environment/?utm_source=chatgpt.com)

Advanced configuration for local development and deployment.

My recommendation: Learn `uv init`, `uv add`, `uv remove`, `uv run`, `uv sync`, `uv lock`, `uv tree`, `uv python`, `uv venv`, and `uv pip` first. These cover most everyday Python development. Then move to dependency groups, workspaces, script metadata, package publishing, and CI/CD locking once you are comfortable building and maintaining projects.
