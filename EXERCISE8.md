# Exercise 8: Creating a "Hello World" Jenkins Job

## Objective

Store a simple shell script in a GitHub repository, then create a Jenkins Freestyle job that pulls the repository and runs the script.

## Prerequisites

- Jenkins running in a Docker container and reachable at `http://localhost:8081` (see the Jenkins installation exercise)
- A GitHub account
- Git installed locally

**Note:** Port 8080 was already in use, so port 8081 was used to access Jenkins.

---

## Part 1: Put the script on GitHub

### 1. Create a GitHub repository

1. Log in to [github.com](https://github.com).
2. Click **+** (top right) → **New repository**.
3. Fill in:
   - **Repository name:** `devops-sample-code`
   - **Description:** A demo repository for Jenkins scripting.
   - **Visibility:** Public
4. Click **Create repository**.

Repository used: `https://github.com/Nikitha-Srinivas05/devops-sample-code.git`

### 2. Create a fine-grained personal access token (PAT)

1. Log in to GitHub and navigate to Settings → Developer settings → Personal access tokens → Fine-grained tokens.
2. Click Generate new token and enter the token name and expiration date.
3. Select the repository under Repository access.
4. Set Contents permission to Read and write if pushing code.
5. Generate the token and store it securely. Never share or commit the token.

### 3. Create the script locally

```bash
touch hello-world.sh
```

Add this content to the file:

```bash
#!/bin/bash
echo "Hello, Jenkins!"
```

Make it executable:

```bash
chmod +x hello-world.sh
```

### 4. Initialize a local Git repository

```bash
git init
```

### 5. Add and commit the script

```bash
git add --chmod=+x -- hello-world.sh
git status
git commit -m "Add hello-world.sh"
```

`git status` should show `hello-world.sh` as a new file ready to be committed.

### 6. Link the local repo to GitHub and push

```bash
git remote add origin https://github.com/Nikitha-Srinivas05/devops-sample-code.git
git push -u origin master
```

When prompted, enter your GitHub username, and use the PAT as the password.

> The branch name must match your local branch. If your branch is `main`, push `main` instead (check with `git branch`).

### 7. Verify on GitHub

Open `https://github.com/Nikitha-Srinivas05/devops-sample-code` and confirm `hello-world.sh` is listed with the commit message **Add hello-world.sh**.

---

## Part 2: Create the Jenkins job

### Step 1: Access Jenkins

Open `http://localhost:8081` and log in with your admin credentials.

### Step 2: Create a new job

1. On the dashboard, click **New Item**.
2. Enter the name `HelloWorld`.
3. Select **Freestyle project**.
4. Click **OK**.

### Step 3: Configure the job

**General**
- Description: `Hello World! Jenkins job.`

**Source Code Management**
- Select **Git**.
- Repository URL: `https://github.com/Nikitha-Srinivas05/devops-sample-code.git`
- Credentials: none (the repository is public)
- Branch Specifier: `*/main` (use `*/master` if your repository's branch is `master`)

**Build Steps**
- Click **Add build step** → **Execute shell**.
- Enter:

```bash
sh hello-world.sh
```

### Step 4: Save and run

1. Click **Save**.
2. On the job page, click **Build Now**.

### Step 5: View the build output

1. In **Build History**, click the build number (`#1`).
2. Click **Console Output**.

---

## Result: Console output

```
Started by user Nikitha Srinivas

Running as SYSTEM
Building in workspace /var/jenkins_home/workspace/HelloWorld
The recommended git tool is: NONE
No credentials specified
 > git rev-parse --resolve-git-dir /var/jenkins_home/workspace/HelloWorld/.git # timeout=10
Fetching changes from the remote Git repository
 > git config remote.origin.url https://github.com/Nikitha-Srinivas05/devops-sample-code.git # timeout=10
Fetching upstream changes from https://github.com/Nikitha-Srinivas05/devops-sample-code.git
 > git --version # timeout=10
 > git --version # 'git version 2.47.3'
 > git fetch --tags --force --progress -- https://github.com/Nikitha-Srinivas05/devops-sample-code.git +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git rev-parse refs/remotes/origin/main^{commit} # timeout=10
Checking out Revision 108d0d0b494575483b2c34e98041606ffea8c184 (refs/remotes/origin/main)
 > git config core.sparsecheckout # timeout=10
 > git checkout -f 108d0d0b494575483b2c34e98041606ffea8c184 # timeout=10
Commit message: "Add hello-world.sh"
First time build. Skipping changelog.
[HelloWorld] $ /bin/sh -xe /tmp/jenkins5713156953007942423.sh
+ sh hello-world.sh
Hello, Jenkins!
Finished: SUCCESS

```

## What the log shows

| Log section | Meaning |
|-------------|---------|
| `Started by user admin` | The build was triggered manually via **Build Now** |
| `Building in workspace /var/jenkins_home/workspace/HelloWorld` | Jenkins created a workspace for the job inside the container |
| `No credentials specified` | No login was needed because the repo is public |
| `git fetch ...` | Jenkins fetched the code from GitHub |
| `Checking out Revision 108d0d0... (refs/remotes/origin/main)` | It checked out the latest commit on `main` |
| `Commit message: "Add hello-world.sh"` | The commit message for the fetched code. |
| `First time build. Skipping changelog.` | There is no previous build to compare against |
| `+ sh hello-world.sh` | The Execute shell step ran the script (`-x` echoes each command) |
| `Hello, Jenkins!` | Output produced by the script |
| `Finished: SUCCESS` | The build completed successfully |

