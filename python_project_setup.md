# Python Project Setup

A well-organized Python project requires several key components to ensure maintainability, reproducibility, and collaboration. This concerns essential aspects including:

- *Project structure and organization
- *Virtual environments for isolation
- *Dependency management
- Development tools (IDE, linting, formatting)
- Testing and documentation
- Version control

*Covered in this document

## Project Structure

Different types of Python projects benefit from different structures. The most relevant for us is the (data) science project structure. For completeness, we'll also look at the modern package structure and possible application structures, and point out some key principles for all structures.

### (Data) Science Project Structure
```
project_name/
├── .venv/              # Virtual environment
├── notebooks/          # Jupyter notebooks
├── src/               # Source code
│   ├── data/         # Data processing scripts
│   ├── features/     # Feature engineering
│   ├── models/       # Model definitions
│   └── visualization/# Visualization code
├── data/             # Data directory
│   ├── raw/         # Original data
│   ├── processed/   # Processed data
│   └── external/    # External data sources
├── models/          # Trained models
├── reports/         # Generated reports
│   ├── figures/    # Generated figures
│   └── results/    # Analysis results
├── tests/           # Test files
├── requirements.txt # Dependencies
├── README.md       # Documentation
└── .gitignore     # Git ignore file
```

### Modern Package Structure (using pyproject.toml)
```
project_name/
├── .venv/                  # Virtual environment (not in version control)
├── src/                    # Source code
│   └── project_name/      # Main package directory
├── tests/                 # Test files
├── docs/                  # Documentation
├── pyproject.toml        # Project metadata and build configuration
├── README.md             # Project documentation
└── .gitignore           # Git ignore file
```

Example `pyproject.toml`:
```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "project_name"
version = "0.1.0"
description = "Project description"
authors = [
    { name = "Your Name", email = "your.email@example.com" }
]
dependencies = [
    "requests>=2.28.0",
    "pandas>=2.0.0",
]
requires-python = ">=3.8"

[project.optional-dependencies]
dev = [
    "pytest>=7.0",
    "black>=22.0",
    "isort>=5.0",
    "mypy>=1.0",
]

[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]

[tool.black]
line-length = 88
target-version = ["py38"]

[tool.isort]
profile = "black"
multi_line_output = 3
```

Key advantages of using pyproject.toml (over setup.py):
- Single file for all project metadata and configuration
- Standardized build system interface (PEP 517)
- Better dependency management
- Integrated tool configuration
- More maintainable than setup.py
- Better support for modern Python packaging tools

### Application Structure (for web apps, CLI tools, etc.)
```
app_name/
├── .venv/                # Virtual environment
├── app/                  # Application code
│   ├── __init__.py
│   ├── main.py          # Entry point
│   ├── config.py        # Configuration
│   ├── models/          # Data models
│   ├── views/           # UI components
│   └── utils/           # Utility functions
├── tests/               # Test files
├── static/              # Static assets
├── templates/           # Template files
├── data/               # Application data
├── requirements.txt    # Dependencies
├── README.md          # Documentation
└── .gitignore        # Git ignore file
```

### Key Principles for All Structures
1. **Separation of Concerns**
   - Keep different types of code in separate directories
   - Maintain clear boundaries between components

2. **Configuration Management**
   - Keep configuration separate from code
   - Use environment variables for sensitive data
   - Store configuration templates in version control

3. **Data Management**
   - Keep data separate from code
   - Use appropriate data directories for different data types
   - Include data processing pipelines

4. **Testing Organization**
   - Mirror the source code structure in tests
   - Keep test data separate from production data
   - Include different types of tests (unit, integration, etc.)

5. **Documentation**
   - Keep documentation close to the code it describes
   - Include setup and usage instructions
   - Document data schemas and APIs

## Virtual Environment

Virtual environments are isolated Python environments that help manage project-specific dependencies. They prevent conflicts between different projects and ensure reproducibility.

### Built-in venv

```bash
# Create a virtual environment
python -m venv .venv

# Activate the virtual environment
# On Windows:
.venv\Scripts\activate
# On macOS/Linux:
source .venv/bin/activate

# Deactivate when done
deactivate
```

### Using virtualenv

virtualenv is a third-party tool that provides more features than the built-in venv module:

```bash
# Install virtualenv
pip install virtualenv

# Create a virtual environment
virtualenv .venv

# Create with specific Python version
virtualenv .venv --python=python3.11

# Create with additional options
virtualenv .venv --no-site-packages --prompt="(myproject)"

# Activate (same as venv)
source .venv/bin/activate  # On macOS/Linux
.venv\Scripts\activate     # On Windows
```

Key advantages of virtualenv:

- More configuration options than venv
- Can create environments with different Python versions
- Supports custom prompt names
- Can exclude system packages
- Works on older Python versions
- Widely used in legacy projects

## Tools combining virtual environments and dependency management

There are several tools that combine virtual environments and dependency management. This is very convenient, because these things go hand in hand. We'll cover a few popular choices below, under 'Dependency Management'.

## Container-based Environment Isolation

Containers provide complete isolation of your Python environment, including the operating system and system dependencies. Containerization in general is a powerful tool for development and deployment, which we'll cover in more detail in a later tutorial. For now, I'll leave you with a few key benefits of containerization:

- Complete environment isolation
- Reproducible builds across different machines
- Easy deployment and scaling
- Consistent development and production environments
- Simple dependency management including system libraries
- Great for microservices architecture



## Dependency Management

Python projects typically use two types of dependency files:
1. `requirements.txt` - Lists all project dependencies
2. `requirements-dev.txt` - Additional dependencies for development

### Managing Dependencies
```bash
# Install dependencies
pip install -r requirements.txt

# Install development dependencies
pip install -r requirements-dev.txt

# Generate requirements file
pip freeze > requirements.txt
```

### Best Practices
- Keep virtual environment and dependency management separate but complementary
- Use `requirements.txt` for production dependencies
- Use `requirements-dev.txt` for development tools (testing, linting, etc.)
- Specify version numbers for dependencies to ensure reproducibility
- Consider using tools like `pip-tools` for dependency management

## Modern Dependency Management Tools

While pip and requirements.txt are the traditional approach, several modern tools offer more robust dependency management:

### uv
uv is a new, extremely fast Python package installer and resolver:
```bash
# Install uv
pip install uv

# Create a virtual environment and install dependencies
uv venv
uv pip install -r requirements.txt

# Install a package
uv pip install requests
```

Key features:
- Much faster than traditional pip
- Written in Rust for performance
- Compatible with existing requirements.txt
- Modern dependency resolution
- Manages virtual environments automatically

### Conda
Conda is a powerful package and environment management system that works across multiple languages, with strong support for Python:
```bash
# Create a new conda environment
conda create --name myenv python=3.11

# Activate the environment
conda activate myenv

# Install packages
conda install numpy pandas scipy

# Export environment
conda env export > environment.yml

# Create environment from file
conda env create -f environment.yml
```

Key features:
- Cross-platform package management
- Handles non-Python dependencies
- Environment management built-in
- Strong support for scientific computing packages
- Works well with Jupyter notebooks
- Can use both conda and pip packages

### Poetry
Poetry is a modern dependency management and packaging tool that simplifies Python project management. It's probably overkill outside of professional software engineering projects though. Basic usage looks like this:
```bash
# Install Poetry
curl -sSL https://install.python-poetry.org | python3 -

# Initialize a new project
poetry new my-project

# Add dependencies
poetry add requests
poetry add --dev pytest

# Install dependencies
poetry install
```

Key features:
- Dependency resolution
- Lock file for reproducible installations
- Virtual environment management
- Build and publish packages
- Project scaffolding


### When to Choose Which Tool?

You can roughly think of the tools as follows:

- Use **pip + requirements.txt** for:
  - Simple projects
  - Projects that need to maintain compatibility with older tools
  - Teams familiar with traditional Python packaging

- Use **uv** for:
  - A good balance between speed and ease of use
  - Built-in support for virtual environments
  - Projects that need faster dependency installation
  - Teams that want to keep using requirements.txt but with better performance
  - CI/CD pipelines where speed matters

- Use **Conda** for:
  - Scientific computing projects
  - Projects requiring non-Python dependencies
  - Teams working with data science tools
  - Projects that need to manage multiple Python versions
  - Cross-platform development with complex dependencies

- Use **Poetry** for:
  - Modern Python projects
  - Projects that need robust dependency management
  - Teams that want an all-in-one solution


