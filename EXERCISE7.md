# Exercise 7: Installing Jenkins with Docker

## Objective

Install Jenkins (LTS) as a Docker container, retrieve the initial admin password, and complete the first-time setup wizard to reach the Jenkins dashboard.

## Prerequisites

- Docker installed and running
- Ports `8081` (web UI) and `50000` (agent communication) free on the host
- A web browser

---

## Step 1: Run the Jenkins container

```bash
docker run -d --name jenkins -p 8081:8080 -p 50000:50000 jenkins/jenkins:lts
```

| Flag | Purpose |
|------|---------|
| `-d` | Run the container in the background (detached) |
| `--name jenkins` | Name the container `jenkins` |
| `-p 8081:8080` | Map the Jenkins web UI to host port 8081 |
| `-p 50000:50000` | Map the port used by Jenkins agents |
| `jenkins/jenkins:lts` | Official Jenkins Long-Term Support image |

**Note:** Port 8080 was already in use, so port 8081 was used to access Jenkins.

**Result:** Docker returned the full container ID, confirming the container was created:

```
091698cab8dce22b4a0ce8fd0ab1eebcb2ec15e74e34d55a689c4e85b40a796a
```

## Step 2: Verify the container is running

```bash
docker ps -a
```

**Result:**

```
CONTAINER ID   IMAGE                                 COMMAND                   CREATED          STATUS                    PORTS                                                                              NAMES
091698cab8dc   jenkins/jenkins:lts                   "/usr/bin/tini -- /u…"    20 seconds ago   Up 19 seconds             0.0.0.0:50000->50000/tcp, [::]:50000->50000/tcp, 0.0.0.0:8081->8080/tcp, [::]:8081->8080/tcp   jenkins
```

The status is `Up`, and both ports are mapped to the host.

## Step 3: Get the initial admin password

Open a shell inside the container:

```bash
docker exec -it 091698cab8dc bash
```

Print the password:

```bash
jenkins@091698cab8dc:/$ cat /var/jenkins_home/secrets/initialAdminPassword
```

Copy the value that is printed (a 32-character hexadecimal string). It is needed in the next step.

## Step 4: Unlock Jenkins

Browse to **http://localhost:8081/**. Jenkins asks for the administrator password. Paste the value from Step 3 and click **Continue**.

![Unlock Jenkins screen](images/exercise7-jenkins-unlock.png)

## Step 5: Install plugins (Getting Started)

Choose **Install suggested plugins**. Jenkins downloads and installs the recommended set, including Folders, Pipeline, Git, Credentials Binding, Timestamper, Gradle, Email Extension, Matrix Authorization Strategy, and others.

![Getting Started plugin installation](images/exercise7-jenkins-getting-started.png)

## Step 6: Create the admin user and finish setup

After the plugins finish installing:

1. Create the first admin user (username, password, full name, email).
2. Confirm the Jenkins URL (`http://localhost:8081/`).
3. Click **Start using Jenkins**.

![Create admin](images/exercise7-jenkins-create-admin.png)

## Step 7: Confirm the Jenkins dashboard

The setup is complete when the **Welcome to Jenkins!** dashboard loads. It shows the Build Queue (empty), the Build Executor Status (0/2), and the **Create a job** option. The footer shows the installed version, **Jenkins 2.580.1**, and the logged-in user is shown in the top-right menu.

![Jenkins dashboard](images/exercise7-jenkins-landing-page.png)

---

## Results summary

| Check | Outcome |
|-------|---------|
| Jenkins LTS image pulled and container started | Success |
| Container status in `docker ps -a` | `Up`, ports 8081 and 50000 mapped |
| Initial admin password retrieved | Success |
| Unlock screen passed | Success |
| Suggested plugins installed | Success |
| Dashboard reachable at `localhost:8081` | Success (Jenkins 2.580.1) |
