# Git Push Strategy Guide

This guide outlines the step-by-step strategy for preparing, committing, and pushing changes to a remote Git repository, including handling large files (>100MB) using Git LFS.

## Step-by-Step Push Strategy

### 1. Check Current Status
Before staging or committing any files, check the state of your working directory and staging area:
```bash
git status
```
This shows:
- Uncommitted changes (modified files)
- Untracked files
- Currently checked-out branch

### 2. Stage the Changes
Add the files you want to include in your commit. 
- To add specific files:
  ```bash
  git add path/to/file
  ```
- To add all modified and untracked files:
  ```bash
  git add .
  ```

### 3. Commit the Changes
Create a local commit with a descriptive message summarizing your changes:
```bash
git commit -m "Your descriptive commit message"
```

### 4. Verify the Remote Configuration
Verify that the remote repository URL (usually named `origin`) is configured correctly:
```bash
git remote -v
```

### 5. Fetch and Integrate Remote Changes (Avoid Conflicts)
To ensure your local branch is up-to-date and prevent conflict issues when pushing, pull the latest changes from the remote:
```bash
git pull --rebase origin <branch-name>
```
> [!NOTE]
> Replace `<branch-name>` with your active branch (e.g., `main` or `master`). Using `--rebase` keeps a clean, linear project history.

### 6. Push to the Remote Repository
Push your local commits to the remote repository:
```bash
git push origin <branch-name>
```
If this is the first time you are pushing this branch, set the upstream:
```bash
git push -u origin <branch-name>
```

---

## Strategy for Pushing Large Files (>100MB)

GitHub has a strict file size limit of 100MB. If you have already committed a file that exceeds this limit (like `system design.zip`), standard `git push` will fail. You must use **Git LFS (Large File Storage)** to track it.

### How to Fix and Push an Already-Committed Large File

Since the large file is already in your local git history, follow these steps to migrate it to LFS:

#### Option A: Using `git lfs migrate` (Recommended)
This command rewrites your local commit history to replace the large file with an LFS pointer.

1. **Initialize Git LFS in your repository:**
   ```bash
   git lfs install
   ```

2. **Migrate the large file in your commit history:**
   ```bash
   git lfs migrate import --include="system design.zip"
   ```
   *(Or track all zip files: `git lfs migrate import --include="*.zip"`)*

3. **Verify LFS tracking status:**
   ```bash
   git lfs ls-files
   ```
   You should see `system design.zip` listed here.

4. **Push your changes:**
   ```bash
   git push origin master
   ```

#### Option B: Resetting and Re-committing (Alternative)
If you want to manually reset your last commit and set up LFS tracking:

1. **Reset the commit but keep the file changes:**
   ```bash
   git reset HEAD~1
   ```

2. **Initialize Git LFS:**
   ```bash
   git lfs install
   ```

3. **Track the large file pattern:**
   ```bash
   git lfs track "system design.zip"
   ```
   This creates or updates a `.gitattributes` file.

4. **Stage the track configuration and files:**
   ```bash
   git add .gitattributes
   git add "system design.zip"
   ```

5. **Commit the changes:**
   ```bash
   git commit -m "Add system design zip using git lfs"
   ```

6. **Push to the remote branch:**
   ```bash
   git push origin master
   ```
