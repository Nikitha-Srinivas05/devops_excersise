# Exercise 9: Jenkins Multi-Stage Pipeline — Deploying a Python Flask Application

**Repository:** https://github.com/Nikitha-Srinivas05/devops-sample-code  
**Jenkins Job:** `Python-MultiStage-Pipeline`  
**Environment:** Jenkins LTS running in Docker on Windows (PowerShell)  
**Branch:** `main`

---

## 1. Objective

To create and execute a Jenkins Declarative Pipeline that automates the build, testing, deployment, and execution of a Python Flask application.

The pipeline consists of five stages:

| Stage | Purpose |
|---|---|
| Build | Create a Python virtual environment and install dependencies |
| Test | Run unit tests for the Flask application |
| Deploy | Copy application files to a deployment directory |
| Run Application | Start the Flask application in the background |
| Test Application | Verify the running application using an HTTP request |

---

## 2. Project Structure

The following structure was maintained in the GitHub repository:

```text
devops-sample-code/
└── python-flask-app/
    ├── app.py
    ├── requirements.txt
    ├── test_app.py
    └── Jenkinsfile
```

The Jenkinsfile was configured at `python-flask-app/Jenkinsfile`.

---

## 3. Sample Application

### `app.py`

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello, Jenkins Multi-Stage Pipeline!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000, debug=False)
```

The Flask application defines a home route (`/`) that returns a welcome message. The application listens on port 5000 when started.

### `test_app.py`

```python
import unittest
from app import app


class TestApp(unittest.TestCase):
    def test_home(self):
        tester = app.test_client()
        response = tester.get("/")

        self.assertEqual(response.status_code, 200)
        self.assertEqual(
            response.data.decode("utf-8"),
            "Hello, Jenkins Multi-Stage Pipeline!"
        )


if __name__ == "__main__":
    unittest.main()
```

The unit test verifies that the home route returns HTTP status code 200 and the expected response message.

### `requirements.txt`

The final contents of the dependency file were:

```text
Flask==2.1.2
Werkzeug==2.1.2
```

Werkzeug was pinned to version 2.1.2 to maintain compatibility with Flask 2.1.2.

---

## 4. Jenkins Environment Setup

Jenkins was running in a Docker container named `jenkins`, with the dashboard accessible at:

`http://localhost:8081`

Python was not available in the Jenkins container initially, so Python and the required packages were installed inside the container.

The container was accessed using PowerShell:

```powershell
docker exec -u root -it jenkins bash
```

The following commands were executed inside the container:

```bash
apt-get update
apt-get install -y python3 python3-pip python3-venv
```

Python was verified using:

```bash
python3 --version
```

The Jenkins container was running Python 3.13. The pipeline subsequently created a separate virtual environment for the Flask application and installed its dependencies.

---

## 5. Jenkins Job Configuration

A Jenkins Pipeline job named `Python-MultiStage-Pipeline` was created with the following configuration:

| Setting | Value |
|---|---|
| Job Name | `Python-MultiStage-Pipeline` |
| Definition | Pipeline script from SCM |
| SCM | Git |
| Repository URL | `https://github.com/Nikitha-Srinivas05/devops-sample-code.git` |
| Branch Specifier | `*/main` |
| Script Path | `python-flask-app/Jenkinsfile` |
| Credentials | Not required for the public repository |

After saving the configuration, **Build Now** was selected to execute the pipeline.

---

## 6. Jenkins Pipeline Execution

The Jenkins Declarative Pipeline was configured with five stages to automate the build, testing, deployment, and verification of the Flask application.

### Jenkinsfile

The Jenkinsfile is available at `python-flask-app/Jenkinsfile` in the repository.

### Stage-wise Results

| Stage | Description | Result |
|---|---|---|
| Build | Created a Python virtual environment and installed the required dependencies. | SUCCESS |
| Test | Executed the unit tests using Python's `unittest` framework. | SUCCESS |
| Deploy | Copied `app.py` and `requirements.txt` to the deployment directory. | SUCCESS |
| Run Application | Started the Flask application in the background and verified that it was accessible. | SUCCESS |
| Test Application | Used `curl` to verify the application's HTTP response. | SUCCESS |

---

## 7. Dependency Configuration

During testing, a compatibility issue occurred between Flask 2.1.2 and Werkzeug 3.1.9, resulting in the following error:

```text
ImportError: cannot import name 'url_quote' from 'werkzeug.urls'
```

The issue was resolved by updating `requirements.txt` to pin Werkzeug to a compatible version.

Final `requirements.txt`:

```text
Flask==2.1.2
Werkzeug==2.1.2
```

---

## 8. Final Execution Results

The pipeline successfully checked out the latest code from the `main` branch and completed all five stages.

### Unit Test Output

```text
.
----------------------------------------------------------------------
Ran 1 test in 0.018s

OK
Hello, Jenkins Multi-Stage Pipeline!
```

### Application Verification

The Flask application started successfully, and the HTTP request returned the expected response:

```text
Hello, Jenkins Multi-Stage Pipeline!
```

### Final Console Output

```text
Started by user Nikitha Srinivas

Obtained python-flask-app/Jenkinsfile from git https://github.com/Nikitha-Srinivas05/devops-sample-code.git
[Pipeline] Start of Pipeline
[Pipeline] node
Running on Jenkins
 in /var/jenkins_home/workspace/Python-MultiStage-Pipeline
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Declarative: Checkout SCM)
[Pipeline] checkout
Selected Git installation does not exist. Using Default
The recommended git tool is: NONE
No credentials specified
 > git rev-parse --resolve-git-dir /var/jenkins_home/workspace/Python-MultiStage-Pipeline/.git # timeout=10
Fetching changes from the remote Git repository
 > git config remote.origin.url https://github.com/Nikitha-Srinivas05/devops-sample-code.git # timeout=10
Fetching upstream changes from https://github.com/Nikitha-Srinivas05/devops-sample-code.git
 > git --version # timeout=10
 > git --version # 'git version 2.47.3'
 > git fetch --tags --force --progress -- https://github.com/Nikitha-Srinivas05/devops-sample-code.git +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git rev-parse refs/remotes/origin/main^{commit} # timeout=10
Checking out Revision f1c2b5d1bdd8c1fde183294a481fbbdbd2ac4946 (refs/remotes/origin/main)
 > git config core.sparsecheckout # timeout=10
 > git checkout -f f1c2b5d1bdd8c1fde183294a481fbbdbd2ac4946 # timeout=10
Commit message: "Update requirements.txt"
 > git rev-list --no-walk f875e54ecb07bfbe6ec721d65515edc37c517972 # timeout=10
[Pipeline] }
[Pipeline] // stage
[Pipeline] withEnv
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Build)
[Pipeline] dir
Running in /var/jenkins_home/workspace/Python-MultiStage-Pipeline/python-flask-app
[Pipeline] {
[Pipeline] echo
Creating virtual environment and installing dependencies...
[Pipeline] sh
+ python3 -m venv venv
+ venv/bin/python -m pip install --upgrade pip
Requirement already satisfied: pip in ./venv/lib/python3.13/site-packages (26.2.1)
+ venv/bin/python -m pip install -r requirements.txt
Requirement already satisfied: flask==2.1.2 in ./venv/lib/python3.13/site-packages (from -r requirements.txt (line 1)) (2.1.2)
Collecting Werkzeug==2.1.2 (from -r requirements.txt (line 2))
  Downloading Werkzeug-2.1.2-py3-none-any.whl.metadata (4.4 kB)
Requirement already satisfied: Jinja2>=3.0 in ./venv/lib/python3.13/site-packages (from flask==2.1.2->-r requirements.txt (line 1)) (3.1.6)
Requirement already satisfied: itsdangerous>=2.0 in ./venv/lib/python3.13/site-packages (from flask==2.1.2->-r requirements.txt (line 1)) (2.2.0)
Requirement already satisfied: click>=8.0 in ./venv/lib/python3.13/site-packages (from flask==2.1.2->-r requirements.txt (line 1)) (8.5.0)
Requirement already satisfied: MarkupSafe>=2.0 in ./venv/lib/python3.13/site-packages (from Jinja2>=3.0->flask==2.1.2->-r requirements.txt (line 1)) (3.0.4)
Downloading Werkzeug-2.1.2-py3-none-any.whl (224 kB)
Installing collected packages: Werkzeug
  Attempting uninstall: Werkzeug
    Found existing installation: Werkzeug 3.1.9
    Uninstalling Werkzeug-3.1.9:
      Successfully uninstalled Werkzeug-3.1.9
Successfully installed Werkzeug-2.1.2
[Pipeline] }
[Pipeline] // dir
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Test)
[Pipeline] dir
Running in /var/jenkins_home/workspace/Python-MultiStage-Pipeline/python-flask-app
[Pipeline] {
[Pipeline] echo
Running unit tests...
[Pipeline] sh
+ venv/bin/python -m unittest discover -s . -p test_*.py
.
----------------------------------------------------------------------
Ran 1 test in 0.018s

OK
Hello, Jenkins Multi-Stage Pipeline!
[Pipeline] }
[Pipeline] // dir
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Deploy)
[Pipeline] dir
Running in /var/jenkins_home/workspace/Python-MultiStage-Pipeline/python-flask-app
[Pipeline] {
[Pipeline] echo
Deploying application...
[Pipeline] sh
+ mkdir -p /var/jenkins_home/workspace/Python-MultiStage-Pipeline/python-app-deploy
+ cp app.py /var/jenkins_home/workspace/Python-MultiStage-Pipeline/python-app-deploy/
+ cp requirements.txt /var/jenkins_home/workspace/Python-MultiStage-Pipeline/python-app-deploy/
[Pipeline] }
[Pipeline] // dir
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Run Application)
[Pipeline] echo
Starting Flask application...
[Pipeline] sh
+ echo 1671
+ nohup /var/jenkins_home/workspace/Python-MultiStage-Pipeline/python-flask-app/venv/bin/python /var/jenkins_home/workspace/Python-MultiStage-Pipeline/python-app-deploy/app.py
+ seq 1 15
+ curl -fsS http://127.0.0.1:5000/
curl: (7) Failed to connect to 127.0.0.1 port 5000 after 0 ms: Could not connect to server
+ sleep 1
+ curl -fsS http://127.0.0.1:5000/
+ echo Application started successfully.
Application started successfully.
+ exit 0
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Test Application)
[Pipeline] echo
Testing deployed application...
[Pipeline] sh
+ curl -fsS http://127.0.0.1:5000/
Hello, Jenkins Multi-Stage Pipeline!
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Declarative: Post Actions)
[Pipeline] sh
+ [ -f /var/jenkins_home/workspace/Python-MultiStage-Pipeline/python-app-deploy/app.pid ]
+ cat /var/jenkins_home/workspace/Python-MultiStage-Pipeline/python-app-deploy/app.pid
+ kill 1671
[Pipeline] echo
Pipeline completed successfully!
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline
Finished: SUCCESS

```

---

## 9. Conclusion

The Jenkins Declarative Pipeline was successfully implemented to automate the build, testing, deployment, and verification of a Python Flask application. A dependency compatibility issue between Flask and Werkzeug was resolved by specifying compatible versions in `requirements.txt`.

All five pipeline stages completed successfully, demonstrating a basic CI/CD workflow using Jenkins, Docker, GitHub, Python virtual environments, and automated testing.

**Final Result: SUCCESS**
