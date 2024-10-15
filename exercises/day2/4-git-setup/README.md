# Exercise 4: Git and GitHub Config

In this exercise, we'll learn to create source control system AKA git / git_hub

To accomplish this, we'll create a github account

### Setup Github account

Step-by-step exercise for creating a GitHub account, setting up a sample GitHub repository, and using Git on Windows to clone, modify, commit, and push changes.

---

### Exercise: Creating a GitHub Account, Repository, and Using Git on Windows

#### Prerequisites:
- Ensure **Git/Git Bash** is installed on your Windows machine.
- Have **Notepad++** or any VS CODE for code editing.

#### Part 1: Create a GitHub Account and Repository
1. **Create a GitHub account:**
   - Visit [GitHub](https://github.com/) and sign up for a free account.
   - Complete the registration process by confirming your email.

2. **Create a new repository:**
   - After logging in, click on the **+** icon in the upper-right corner and select **New repository**.
   - Set the following options:
     - **Repository name**: `sample-repo`
     - **Description**: "Sample Git repository for Git exercise"
     - **Public or Private**: Select **Public** or **Private** (Public is recommended for easier sharing)
     - **Initialize with README**: Check this box.
   - Click **Create repository**.

#### Part 2: Clone the Repository to Your Local Machine
3. **Open Git Bash on Windows**:
   - Navigate to the directory where you want to clone the repository.
   - Open **Git Bash** (or use **Command Prompt** if Git is configured).

4. **Clone the repository**:
   - In your GitHub repo page, click on the **Code** button and copy the repository URL.
   - In Git Bash, type:
     ```bash
     git clone <repository URL>
     ```
   - Example:
     ```bash
     git clone https://github.com/your-username/sample-repo.git
     ```
   - This will download the repository to your local machine.

#### Part 3: Modify the Repository Locally
5. **Navigate to the repository folder**:
   ```bash
   cd sample-repo
   ```

6. **Create or modify a file**:
   - Open a text editor and create a file named `sample.txt`.
   - Add the following content:
     ```
     Hello, this is a test file for Git exercise.
     ```

7. **Save the file** to your `sample-repo` folder.

#### Part 4: Stage, Commit, and Push Changes to GitHub
8. **Stage the file** for commit:
   - In Git Bash, run the following command to add the new file to the staging area:
     ```bash
     git add sample.txt
     ```

9. **Commit the changes**:
   - Run the following command to commit the changes with a message:
     ```bash
     git commit -m "Added sample.txt file for Git exercise"
     ```

10. **Push the changes** to GitHub:
    - Push the committed changes to your GitHub repository by running:
      ```bash
      git push origin main
      ```
    - You will be prompted to enter your GitHub username and password (or use a personal access token if you have enabled two-factor authentication).

#### Part 5: Verify the Changes
11. **Verify the pushed changes on GitHub**:
    - Go to your GitHub repository page and refresh it.
    - You should see the newly added `sample.txt` file with the changes you made.

#### Part 6: Conclusion
- You've successfully created a GitHub account, set up a repository, cloned it to your local machine, modified files, and pushed changes back to GitHub using Git on Windows.

Here’s a continuation exercise that will guide you through using Git branches on your local machine and pushing them to GitHub.

---

### Exercise: Branching in Git

#### Part 1: Creating and Working with Branches

1. **Create a new branch:**
   - First, ensure you're in the directory of your cloned repository.
   - In Git Bash, type the following to create a new branch named `feature-branch`:
     ```bash
     git branch feature-branch
     ```
   - Switch to the new branch:
     ```bash
     git checkout feature-branch
     ```
   - Alternatively, you can create and switch to the branch in one command:
     ```bash
     git checkout -b feature-branch
     ```

2. **Verify you are on the new branch**:
   - Check the current branch:
     ```bash
     git branch
     ```
   - You should see an asterisk (`*`) next to `feature-branch`, indicating that it is the active branch.

#### Part 2: Modify Code in the New Branch

3. **Make changes in the new branch**:
   - Open the `sample.txt` file in your text editor.
   - Add another line to the file:
     ```
     This line was added in the feature-branch.
     ```
   - Save the file.

4. **Stage and commit changes in the branch**:
   - Stage the changes:
     ```bash
     git add sample.txt
     ```
   - Commit the changes:
     ```bash
     git commit -m "Added a line in feature-branch"
     ```

#### Part 3: Push the New Branch to GitHub

5. **Push the branch to GitHub**:
   - Push the newly created branch to GitHub using:
     ```bash
     git push -u origin feature-branch
     ```
   - The `-u` flag sets `origin feature-branch` as the default for future pushes from this branch.

6. **Verify the branch on GitHub**:
   - Go to your GitHub repository in the browser.
   - Click on the **Branch** dropdown to view your new branch, `feature-branch`, listed alongside `main`.
   - Select `feature-branch` to see the changes made in the branch (the additional line in `sample.txt`).

#### Part 4: Merging Branches

7. **Switch back to the `main` branch**:
   - In Git Bash, switch back to the `main` branch:
     ```bash
     git checkout main
     ```

8. **Merge `feature-branch` into `main`**:
   - To merge the changes from `feature-branch` into `main`, run:
     ```bash
     git merge feature-branch
     ```

9. **Resolve merge conflicts (if any)**:
   - If Git encounters any conflicts, it will stop the merge and allow you to manually resolve them.
   - Open the conflicting files and manually edit them to resolve the conflicts.
   - After resolving, add the resolved files using `git add <filename>`, and complete the merge with:
     ```bash
     git commit
     ```

#### Part 5: Push the Merged Changes to GitHub

10. **Push the updated `main` branch** to GitHub:
    - Push the merged `main` branch to GitHub:
      ```bash
      git push origin main
      ```

#### Part 6: Cleaning Up

11. **Delete the feature branch locally** (optional):
    - After merging the branch, you can delete `feature-branch` locally:
      ```bash
      git branch -d feature-branch
      ```

12. **Delete the feature branch on GitHub** (optional):
    - You can also delete the branch from GitHub by running:
      ```bash
      git push origin --delete feature-branch
      ```

#### Part 7: Conclusion
- You've successfully created a branch, made changes, merged the branch into `main`, and pushed the changes to GitHub.
  
This exercise demonstrates how to manage branches, a core feature of Git, allowing for better collaboration and feature isolation.


# END