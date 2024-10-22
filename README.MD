# PipeLine Test


### 1. **Create a Personal Access Token (PAT)**

Follow these steps to generate a Personal Access Token:

1. Go to your GitHub account and navigate to **Settings**.
2. In the left sidebar, click on **Developer Settings** > **Personal Access Tokens**.
3. Click on **Generate new token**.
4. Select the scopes your token needs. For example, select:
   - `repo` (full control of private repositories)
   - `workflow` (to update workflows)
5. Generate the token and **copy it**.

### 2. **Add the PAT as a GitHub Secret**

1. In your repository, navigate to **Settings** > **Secrets and variables** > **Actions**.
2. Click on **New repository secret**.
3. Name it something like `PAT_TOKEN`.
4. Paste your Personal Access Token here and click **Add secret**.

### 3. **Modify the Workflow File**

You need to update your workflow file to use the PAT secret for pushing changes.

Update the `add-file.yml` pipeline like this:

```yaml
name: Add File to Repository

on:
  push:
    branches:
      - main

jobs:
  add-file:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v3

    - name: Add new file
      run: |
        echo "This is a new file" > newfile.txt

    - name: Commit and push changes
      env:
        TOKEN: ${{ secrets.PAT_TOKEN }}  # Use the secret here
      run: |
        git config --global user.name "github-actions[bot]"
        git config --global user.email "github-actions[bot]@users.noreply.github.com"
        git add newfile.txt
        git commit -m "Add new file via GitHub Actions"
        git remote set-url origin https://x-access-token:${TOKEN}@github.com/${{ github.repository }}.git
        git push
```

### Explanation:

- **TOKEN: ${{ secrets.PAT_TOKEN }}**: The `TOKEN` variable is populated with the PAT secret you added to the repository.
- **git remote set-url**: This command changes the remote URL to include the token for authentication, allowing GitHub Actions to push changes back to the repository.

### 4. **Commit & Push**

After making these changes, commit the new workflow and push it to your repository. The GitHub Actions workflow should now have the necessary permissions to push changes using the token.
