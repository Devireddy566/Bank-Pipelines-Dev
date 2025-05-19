# Configuring a Self-Hosted Runner for Access to All Repositories

## Introduction
This document provides a guide on setting up a self-hosted GitHub Actions runner that can access all repositories owned by your user account or organization. This is useful when you have workflows that need to interact with multiple repositories.

## Prerequisites
*   A machine (physical or virtual, Linux, macOS, or Windows) to host the runner.
*   Administrative access to the GitHub account or organization where the repositories are located.
*   Basic familiarity with the command line interface (CLI) of your runner machine's OS.

## Steps

### 1. Choose an Authentication Method
To allow the runner to access your repositories, it needs to authenticate with GitHub. You have two main options:

*   **Personal Access Token (PAT):**
    *   **Pros:** Simpler to set up for accessing all repositories under an account.
    *   **Cons:** If compromised, a PAT can grant broad access. Must be stored securely.
    *   **How:**
        1.  Go to your GitHub **Settings**.
        2.  Navigate to **Developer settings** > **Personal access tokens** > **Tokens (classic)**.
        3.  Click **Generate new token** (or **Generate new token (classic)**).
        4.  Give your token a descriptive name (e.g., `self-hosted-runner-all-repos`).
        5.  Set an appropriate **Expiration**.
        6.  Select the **`repo`** scope (Full control of private repositories). This is crucial for accessing all your repositories.
        7.  Click **Generate token**.
        8.  **Important:** Copy the token immediately. You won't be able to see it again. Store it securely.

*   **SSH Keys:**
    *   **Pros:** Can be more secure if managed properly, especially if you want to avoid PATs with broad permissions.
    *   **Cons:** More complex if you need the runner to access *all* repositories under a personal account, as deploy keys are per-repository. For organization runners, an SSH key associated with a machine account that has access to all org repos can work.
    *   **How (for a user account):**
        1.  Generate an SSH key pair on the runner machine: `ssh-keygen -t ed25519 -C "your_email@example.com"`
        2.  Add the public key (`~/.ssh/id_ed25519.pub`) to your GitHub account's SSH keys: Go to **Settings** > **SSH and GPG keys** > **New SSH key**.
        3.  Ensure your runner's Git operations use this SSH key.

For accessing *all* repositories, a **PAT with `repo` scope is generally the more straightforward method** for self-hosted runners, especially when workflows need to clone various repositories.

### 2. Install the GitHub Actions Runner Software
You'll register the self-hosted runner at either the organization level (to access all organization repositories) or at the repository level (if you intend to use a PAT that has access to all your other personal repositories).

*   **For Organization Runners (Recommended for "all repos" access within an org):**
    1.  Go to your GitHub organization's page.
    2.  Click **Settings**.
    3.  In the left sidebar, click **Actions** (under "Code, planning, and automation"), then **Runners**.
    4.  Click **New self-hosted runner**.
    5.  Choose the operating system and architecture of your runner machine.
    6.  Follow the **Download** and **Configure** instructions provided on the page. These typically involve:
        *   Creating a folder for the runner.
        *   Downloading the latest runner package.
        *   Extracting the package.
        *   Running the `config.cmd` (for Windows) or `config.sh` (for Linux/macOS) script.
            *   You will be prompted for the GitHub URL (e.g., `https://github.com/YOUR_ORG`).
            *   You will be prompted for a registration token (provided on the "New self-hosted runner" page).
            *   You can assign labels to your runner (e.g., `self-hosted,my-custom-runner`).

*   **For User Account Runners (accessing all personal repos):**
    *   GitHub doesn't have a concept of "user-level" runners that automatically see all your personal repos. You register a runner to a *specific* repository.
    *   Go to one of your repositories.
    *   Click **Settings** > **Actions** > **Runners** > **New self-hosted runner**.
    *   Follow the same download and configuration steps as above.
    *   The key is that the PAT you use (or the SSH key associated with your account) will grant access to your *other* repositories when a workflow tries to check them out.

**During the `./config.sh` or `config.cmd` process:**
*   The script will ask how you want to authenticate. If you're using a PAT for broad access, you might not enter it directly here. The runner authenticates to register itself. The PAT for repository access is typically used *within* your workflows (e.g., with `actions/checkout`).
*   Ensure the runner service/process is started after configuration (e.g., `run.cmd` or `run.sh`). For long-term use, consider setting it up as a service.

### 3. Granting Access to All Repositories

*   **Organization Runners:**
    1.  After adding a runner to your organization, go to **Organization Settings** > **Actions** > **Runners**.
    2.  Find your runner (it might be in a group, often "Default"). Click on the runner group or the runner itself to manage its settings.
    3.  Under **Repository access** (for the group the runner is in), you can choose:
        *   **All repositories**: Allows the runner group to be used by workflows from any repository in the organization.
        *   **Selected repositories**: Allows you to specify which repositories can use runners in this group.
    4.  For your goal, select **All repositories**.

*   **User Account Runners (using a PAT/SSH Key for access):**
    *   The runner itself is registered to one repository.
    *   Access to *other* repositories is achieved within your workflow files by using your PAT (with `repo` scope) or relying on the SSH key associated with your GitHub account.
    *   The `actions/checkout` action, for example, can use a PAT to clone other private repositories.

### 4. Using the Runner in Workflows
In your workflow YAML files, specify that the job should run on your self-hosted runner using its labels (e.g., `self-hosted`).

```yaml
name: CI Pipeline

on: [push]

jobs:
  build:
    runs-on: self-hosted # Or a more specific label you assigned

    steps:
    - name: Checkout current repository
      uses: actions/checkout@v4

    # Example: Checkout another private repository owned by you
    - name: Checkout another private repo
      uses: actions/checkout@v4
      with:
        repository: YOUR_USERNAME/ANOTHER_PRIVATE_REPO_NAME
        token: ${{ secrets.MY_PAT_WITH_REPO_ACCESS }} # PAT with 'repo' scope
        path: ./another-repo # Optional: checkout to a specific path

    - name: List files
      run: |
        echo "Files in current repo:"
        ls -R
        echo "Files in another-repo:"
        ls -R ./another-repo
```

**Storing your PAT as a Secret:**
1.  In the GitHub repository (or organization) where your workflow resides, go to **Settings** > **Secrets and variables** > **Actions**.
2.  Click **New repository secret** (or **New organization secret**).
3.  Name the secret (e.g., `MY_PAT_WITH_REPO_ACCESS`).
4.  Paste your PAT value into the "Secret" field.
5.  Click **Add secret**.

### 5. Security Considerations

*   **PAT Security:**
    *   **Treat PATs like passwords.** Store them securely.
    *   Use GitHub Actions secrets to store PATs for use in workflows.
    *   Grant only the necessary scopes (`repo` is broad, so be cautious).
    *   Set an expiration date for PATs and rotate them regularly.
*   **SSH Key Security:**
    *   Protect the private SSH key on the runner machine with strong file permissions (e.g., `chmod 600 ~/.ssh/id_ed25519`).
    *   Consider using a dedicated SSH key for the runner if not using your personal one.
*   **Runner Machine Security:**
    *   Keep the runner machine's operating system and all software (including the runner agent itself) up to date.
    *   Run the runner software with the least privileges necessary. Avoid running as root or administrator if possible.
    *   **Only run workflows from trusted sources.** Self-hosted runners execute code from your repositories; ensure you trust the code.
    *   Consider network segmentation and firewall rules to limit the runner machine's access to only what's necessary.
*   **Network Access:**
    *   Ensure the runner machine has outbound HTTPS (port 443) access to:
        *   `github.com`
        *   `api.github.com`
        *   `*.actions.githubusercontent.com` (for downloading actions and logging)
        *   `*.pkg.github.com` (for GitHub Packages)
        *   Any other services your workflows might need to access.

### 6. What to Install on the Runner
The specific software you need to install on your runner machine depends entirely on what your workflows will do. Common requirements include:
*   **Git:** Essential for checking out code. Usually installed by default or as part of the runner setup.
*   **Language Runtimes:** Node.js, Python, Java (JDK), Go, Ruby, .NET SDK, etc., depending on your projects.
*   **Build Tools:** Maven, Gradle, npm/yarn, pip, Docker, etc.
*   **Cloud CLIs:** AWS CLI, Azure CLI, gcloud CLI, if your workflows interact with cloud services.
*   **Testing Frameworks:** Tools relevant to your project's testing needs.
*   **Other Utilities:** `curl`, `jq`, `unzip`, etc.

Install these tools globally on the runner machine or ensure your workflows install them on demand.

## Troubleshooting
*   **Runner not picking up jobs:**
    *   Check if the runner process is running on your machine.
    *   Verify the labels in your workflow's `runs-on` field match the labels of your runner.
    *   Ensure the runner has network connectivity to GitHub.
    *   Check the runner's diagnostic logs (`_diag` folder in the runner directory).
*   **Authentication failures:**
    *   If using a PAT, ensure it's not expired and has the correct `repo` scope.
    *   If using SSH, verify the SSH key is correctly configured on the runner and added to your GitHub account. Test with `ssh -T git@github.com`.
*   **Permission denied when accessing repositories:**
    *   Double-check the PAT scopes or SSH key permissions.
    *   For organization runners, ensure the runner group has access to the target repository.

By following these steps, you can configure a self-hosted runner to efficiently handle workflows across all your GitHub repositories. Remember to prioritize security in your setup.
