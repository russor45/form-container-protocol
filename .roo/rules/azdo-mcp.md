# Azure DevOps Workflow Guide (azdo-mcp) (Revised)

This guide outlines standard workflows for interacting with Azure DevOps, including contributing code changes and reviewing pull requests (PRs), utilizing the available MCP tools (callable as Python functions) and adhering to established code standards.

## Objective

To ensure consistent development practices, high-quality code, maintainability, and adherence to project standards throughout the development lifecycle.

## Contributing Changes

This section covers the standard workflow for committing code, pushing branches, and creating pull requests.

### 1. Committing Changes

Before creating a pull request, ensure your changes are committed with clear, descriptive messages following conventional commit standards.

#### Conventional Commit Standards

All commit messages **MUST** follow the [Conventional Commits](https:/www.conventionalcommits.org) specification. This ensures consistent, readable commit history and enables automated tooling.

**Format:**
```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

**Required Elements:**
- **type**: Describes the kind of change (see types below)
- **description**: Brief summary of the change (lowercase, no period at end)

**Common Types:**
- `feat`: A new feature for the user
- `fix`: A bug fix
- `docs`: Documentation only changes
- `style`: Changes that do not affect the meaning of the code (white-space, formatting, missing semi-colons, etc)
- `refactor`: A code change that neither fixes a bug nor adds a feature
- `perf`: A code change that improves performance
- `test`: Adding missing tests or correcting existing tests
- `chore`: Changes to the build process or auxiliary tools and libraries such as documentation generation
- `ci`: Changes to CI configuration files and scripts
- `build`: Changes that affect the build system or external dependencies

**Examples:**
```bash
feat: add user authentication system
fix: resolve memory leak in data processing
docs: update API documentation for user endpoints
refactor: extract common validation logic into utility functions
test: add unit tests for payment processing
chore: update dependencies to latest versions
```

**With Scope (Optional):**
```bash
feat(auth): implement OAuth2 integration
fix(api): handle null response in user service
docs(readme): add installation instructions
```

**Breaking Changes:**
For breaking changes, add `!` after the type/scope or include `BREAKING CHANGE:` in the footer:
```bash
feat!: remove deprecated user API endpoints
feat(api): add new user endpoint

BREAKING CHANGE: The old /users endpoint has been removed. Use /v2/users instead.
```

#### Commit Process

1.  **Stage Changes**: Ensure the necessary files are staged for the commit. If changes need to be staged, the AI will use the `run_terminal_cmd` tool with the appropriate `git add` command (e.g., `git add <file>...` or `git add .`). The AI may need to ask the user which files to stage if it's unclear.
2.  **Analyze Staged Changes**: The AI will analyze the staged changes (using `git diff --staged | cat`) to understand the modifications.
3.  **Generate Commit Message**: Based on the analysis, the AI will generate a commit message strictly following the conventional commit standards outlined above. The message will:
    - Use the appropriate type based on the nature of changes
    - Include a clear, concise description
    - Add scope when relevant (e.g., component, module, or area affected)
    - Follow the exact format requirements
4.  **Commit**: Execute the commit using the generated conventional commit message:
    ```bash
    git commit -m "Generated conventional commit message"
    ```

### 2. Pushing Your Branch

Push your local changes to the remote repozsitory.

1.  **Check Current Branch**: Identify the current local branch:
    ```bash
    git branch --show-current | cat
    ```
2.  **Verify Branch**: Ensure the current branch is **not** `master`. Pushing directly to `master` is restricted.
3.  **Push Branch**: Push the current branch to the remote origin. Replace `YOUR_BRANCH` with the actual branch name identified in step 1. Pre-push hooks will run unless bypassed manually if needed.
    ```bash
    git push -u origin YOUR_BRANCH
    ```

### 3. Creating the Pull Request

Once changes are committed and pushed, create a pull request to merge them into the target branch (usually `master`). The AI will use tools prefixed with `mcp_azure-devops_` to interact with Azure DevOps.

1.  **Prerequisites Check**:
    *   **Staged Changes**: The AI will run `git diff --staged --quiet || echo "has staged"`. If the output is 'has staged', it means there are uncommitted staged changes. In this case, the AI must repeat the 'Committing Changes' steps and push again before creating the pull request. (The command `git diff --staged --quiet || echo "has staged"` is used to check for staged changes; its output is intentionally brief.)
    *   **Unpushed Commits**: Check if the local branch has commits not present on its remote counterpart (`git log origin/YOUR_BRANCH..YOUR_BRANCH --oneline | cat`). If found, repeat the "Pushing Your Branch" step.
2.  **Identify Target Branch**: The standard target branch is `master`. The AI will verify available remote branches using `git branch -r | cat`. If `master` is not present, or if there's reason to believe another branch should be the target (e.g., based on project conventions or previous user instructions), the AI should confirm the target branch with the user.
3.  **Gather Commit History**: Collect commit messages for the description:
    ```bash
    git log --oneline origin/master..YOUR_BRANCH | cat
    ```
4.  **Get Work Item ID**: If not already provided, **ask the user** for the Azure DevOps Work Item ID associated with these changes. This ID is required for the PR title.
5.  **Create Pull Request via MCP**: Use the gathered information to create the PR.
    *   **Title**: Must start with the Work Item ID in brackets, followed by a concise summary (e.g., `[12345] feat: Implement user profile page`).
    *   **Description**: Summarize the changes based on the commit history gathered in step 3.
    *   If the user has not specified reviewers for the pull request, and it is standard practice to include them, the AI should ask the user if they would like to add any reviewers.
    *   To create the pull request, the AI will use the `mcp_azure-devops_create_pull_request` tool. Here's how it would be called, replacing placeholders with actual values:
        ```python
        # AI will call this tool (example):
        # default_api.mcp_azure-devops_create_pull_request(
        #     title="[12345] feat: Describe the main feature or fix",
        #     description="Summarize the changes included in this PR based on the commit history.\n\nExample (using output from 'git log --oneline'):\n- abc1234 Added feature X\n- def5678 Fixed bug Y\n- Updated documentation.",
        #     sourceBranch="YOUR_BRANCH",
        #     targetBranch="master",
        #     reviewers=["reviewer1@example.com", "reviewer2@example.com"] # Optional
        # )
        ```
6.  **Update Description from Diff (Automatic)**: Immediately after the PR is created (using the `pullRequestId` from the previous step's response), the AI will perform the following:
    *   Fetch the PR diff using `mcp_azure-devops_get_pull_request_diff`. Example call:
        ```python
        # AI will call this tool (example):
        # default_api.mcp_azure-devops_get_pull_request_diff(pullRequestId=12345)
        ```
    *   Analyze the diff content to identify specific changes (new files, modifications, key areas affected).
    *   Generate a revised, more detailed description based on the diff analysis.
    *   Update the pull request with the new description using `mcp_azure-devops_update_pull_request`. Example call:
        ```python
        # AI will call this tool (example):
        # default_api.mcp_azure-devops_update_pull_request(
        #     pullRequestId=12345,
        #     description="New, more detailed description based on diff analysis."
        # )
        ```

## Pull Request Review Guide

This section outlines the process for reviewing pull requests.

### Process Overview

1.  **Fetch Active Pull Requests**: Use the `mcp_azure-devops_list_pull_requests` tool to list active pull requests.
2.  **Select a Pull Request**: Choose a PR to review based on priority or assignment.
3.  **Review the Changes**: Examine the code changes, focusing on logic, correctness, style, and potential issues.
4.  **Reference Code Standards**: Consult the `agent-rules/CODE_STANDARDS.md` file to ensure the changes align with our established guidelines (e.g., naming conventions, component design, state management patterns).
5.  **Provide Feedback**: Use the `mcp_azure-devops_create_pull_request_comment` tool to add comments directly to the PR.
6.  **Submit Review**: Approve, request changes, or reject the PR based on the review findings.

### Using the Azure DevOps MCP Tools (Python Functions)

The tools prefixed with `mcp_azure-devops_` allow the AI to interact with Azure DevOps directly.

#### Listing Pull Requests

To see active pull requests, the AI will use the `mcp_azure-devops_list_pull_requests` tool:
```python
# AI will call this tool (example):
# default_api.mcp_azure-devops_list_pull_requests(status="active")
```

#### Getting Pull Request Details & Diff

To view the details and changes for a specific PR, use the `mcp_azure-devops_get_pull_request` and `mcp_azure-devops_get_pull_request_diff` tools:

```python
# AI will call this tool for details (example):
# default_api.mcp_azure-devops_get_pull_request(pullRequestId=12345)

# AI will call this tool for the diff (example):
# default_api.mcp_azure-devops_get_pull_request_diff(pullRequestId=12345)
```
*(Replace `12345` with the actual Pull Request ID)*

#### Adding Review Comments

Constructive feedback is crucial. Comments should be specific, actionable, and respectful.

**IMPORTANT**: Submit comments directly on the relevant lines or files within the PR for clarity. All comments generated by the AI must be prefixed with `AI Review: `.

Use the `mcp_azure-devops_create_pull_request_comment` tool:

*   **General Comment:**
    ```python
    # AI will call this tool (example):
    # default_api.mcp_azure-devops_create_pull_request_comment(
    #     pullRequestId=12345,
    #     content="AI Review: Overall looks good, but please address the minor points raised."
    # )
    ```

*   **File-Specific Comment:**
    ```python
    # AI will call this tool (example):
    # default_api.mcp_azure-devops_create_pull_request_comment(
    #     pullRequestId=12345,
    #     content="AI Review: Consider refactoring this service to improve testability.",
    #     filePath="path/to/your/service/file.ts"
    # )
    ```

*   **Line-Specific Comment:**
    ```python
    # AI will call this tool (example):
    # default_api.mcp_azure-devops_create_pull_request_comment(
    #     pullRequestId=12345,
    #     content="AI Review: This variable name doesn't follow the naming conventions outlined in CODE_STANDARDS.md. Please use PascalCase for class names.",
    #     filePath="path/to/your/component/file.ts",
    #     lineNumber=42
    # )
    ```
*(Replace `12345`, file paths, line numbers, and content accordingly)*

### Review Focus Areas

*   **Functionality**: Does the code achieve the intended purpose? Are there edge cases missed?
*   **Correctness**: Is the logic sound? Are there potential bugs?
*   **Readability**: Is the code easy to understand? Are variable/function names clear?
*   **Maintainability**: Is the code well-structured? Can it be easily modified or extended?
*   **Performance**: Are there obvious performance bottlenecks?
*   **Security**: Are there potential security vulnerabilities?
*   **Testing**: Are there sufficient unit/integration tests? Do existing tests pass?
*   **Code Standards**: Does the code adhere to `agent-rules/CODE_STANDARDS.md`? (Check naming, structure, patterns, etc.)
*   **Documentation**: Is necessary documentation (comments, READMEs) updated?

### Review Etiquette

*   Be constructive and polite.
*   Explain *why* a change is suggested, referencing standards or best practices.
*   Offer suggestions for improvement where possible.
*   Keep comments focused on the code, not the author.
*   Respond to comments and questions promptly.

By following this guide, we can ensure consistent, high-quality code reviews and contributions that contribute positively to our projects.
