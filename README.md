# Flask CI/CD Automation

This repository contains the provided Flask/MongoDB application used to implement and demonstrate a DevOps CI/CD assignment. The application itself is treated as the application under test; the CI/CD automation, Jenkins configuration, GitHub Actions workflows, testing, staging and production deployment automation, webhook integration, and notification workflow are the focus of this work.

## CI/CD Objectives

- Automate dependency installation and application testing with Jenkins and GitHub Actions.
- Trigger Jenkins automatically from GitHub pushes to `main`.
- Run GitHub Actions CI on `main` and `staging`.
- Deploy validated builds to staging from the `staging` branch.
- Deploy validated release builds to production from version tags such as `v1.0.0`.
- Send success and failure email notifications from the Jenkins pipeline.
- Store sensitive configuration through Jenkins Credentials and GitHub Secrets.

## Application Under Test

The repository contains a Flask application backed by MongoDB. Its existing functionality includes:

- Viewing student records
- Adding student records
- Updating student records
- Deleting student records
- Bootstrap-based web pages
- MongoDB persistence through Flask-PyMongo

These application features are the functionality exercised by the automated tests; they are not presented here as application development work completed for this assignment.

## Technology Stack

| Area | Technology |
|---|---|
| Application | Python, Flask |
| Database | MongoDB, Flask-PyMongo, PyMongo |
| Frontend | HTML, Jinja2, Bootstrap 5 |
| Configuration | python-dotenv, `.env` |
| Testing | pytest |
| CI/CD | Jenkins Declarative Pipeline, GitHub Actions |
| Source Control | Git, GitHub |
| Webhook Integration | GitHub Webhook |
| Notification | Jenkins Mailer with Gmail SMTP/TLS |

## Prerequisites

- Python 3.x
- MongoDB running locally or an accessible MongoDB instance
- Git
- Jenkins LTS
- Jenkins Mailer plugin
- A Gmail SMTP credential configured in Jenkins
- GitHub repository with Actions enabled

## Application Setup

### Clone the repository

```bash
git clone https://github.com/raviveera2305/flask_Practice.git
cd flask_Practice
```

### Create a virtual environment

**Windows PowerShell:**

```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```

**Linux/macOS:**

```bash
python -m venv venv
source venv/bin/activate
```

### Install dependencies

```bash
pip install -r requirements.txt
```

### Configure environment variables

Create a `.env` file in the project root:

```text
MONGO_URI=mongodb://localhost:27017/student_db
SECRET_KEY=your-secret-key
```

Do not commit `.env` or real credentials to source control.

### Run the application

```bash
python app.py
```

The application is available at:

```text
http://localhost:5000
```

## Automated Testing

Run the test suite locally with:

```bash
pytest -v
```

The demonstrated test run completed with all four application tests passing. The same test command is executed by the Jenkins pipeline and GitHub Actions workflow.

**Evidence:** `01-tests-passed-after-mongodb-fix.png`

## Jenkins CI/CD Pipeline

The repository contains a Jenkins Declarative Pipeline in `Jenkinsfile`.

### Pipeline flow

```text
Developer push to GitHub main
            |
            v
      GitHub Webhook
            |
            v
      Jenkins Pipeline
       /     |      \
      v      v       v
    Build   Test    Deploy
      |      |       |
   Install pytest  Staging
   packages suite  deployment
            |
            v
    Success / Failure Email
```

### Pipeline stages

1. **Build** — installs the dependencies from `requirements.txt` using the configured Python interpreter.
2. **Test** — runs `pytest -v` and stops the pipeline if the tests fail.
3. **Deploy** — copies the application to the Windows staging directory and excludes `.git` metadata and local screenshots.
4. **Post-build notification** — sends a success or failure email using Jenkins Mailer.

### Jenkins job configuration

The Jenkins job is configured as a Pipeline using:

- Repository: `raviveera2305/flask_Practice`
- Branch: `main`
- Pipeline definition: `Jenkinsfile`
- Trigger: GitHub hook trigger for GITScm polling

**Evidence:** `02-jenkins-pipeline-configuration.png`

### Build and test evidence

The Jenkins Build and Test stages successfully install the required Python dependencies and execute the automated tests.

**Evidence:** `03-jenkins-build-test-output.png`

### Staging deployment

The pipeline deploys the application to:

```text
C:\Jenkins-Staging\flask_Practice
```

The deployment completes successfully after the Build and Test stages pass.

**Evidence:**

- `04-jenkins-deployment-success.png` — successful staging deployment and pipeline completion.
- `05-staging-deployment-files.png` — deployed application files in the staging directory.

## GitHub Webhook Automation

GitHub push events are integrated with the local Jenkins instance through a webhook. During testing, an ngrok tunnel provided temporary public access to the Jenkins webhook endpoint.

**Evidence:**

- `06-jenkins-github-trigger.png` — Jenkins GitHub hook trigger configuration.
- `07-ngrok-jenkins-tunnel.png` — temporary ngrok forwarding to Jenkins.
- `08-github-webhook-configured.png` — GitHub webhook configuration.
- `09-jenkins-github-auto-trigger.png` — Jenkins automatically triggered by a GitHub push.
- `10-jenkins-auto-build-success.png` — automatically triggered build completed successfully.

The ngrok tunnel was used only for assignment testing and should not be left running unnecessarily.

## Email Notifications

Jenkins Mailer is configured to use Gmail SMTP with TLS on port `587`.

SMTP credentials are stored in Jenkins Credentials and are not committed to the repository.

The pipeline sends:

- A **SUCCESS** notification after a successful pipeline.
- A **FAILURE** notification when the pipeline fails.

**Evidence:**

- `11-jenkins-email-configuration.png` — Jenkins email configuration.
- `12-jenkins-pipeline-success-email.png` — actual Jenkins pipeline SUCCESS notification received by email.

## GitHub Actions CI/CD

GitHub Actions is configured in `.github/workflows/ci-cd.yml`.

### Branch and release strategy

- `staging` — runs Build and Test, then packages and uploads a staging deployment artifact.
- `main` — runs Build and Test for the main development line.
- `v*` release tags — run Build and Test, then package and upload a production deployment artifact.

### GitHub Actions pipeline flow

```text
Push to staging
      |
      v
 Build and Test
      |
      v
Deploy to Staging
      |
      v
staging-deployment artifact
```

```text
Push version tag (for example v1.0.0)
      |
      v
 Build and Test
      |
      v
Deploy to Production
      |
      v
production-deployment artifact
```

### CI configuration

The workflow:

1. Checks out the repository.
2. Sets up Python 3.12.
3. Installs dependencies from `requirements.txt`.
4. Starts MongoDB 7 as a GitHub Actions service.
5. Runs the four automated pytest tests.
6. Compiles `app.py` as a build verification step.
7. Packages the validated application for staging or production as appropriate.

### GitHub Secrets

The workflow uses the repository secret `FLASK_SECRET_KEY` for the test environment instead of storing the secret value in source control.

The secret is referenced securely as:

```yaml
SECRET_KEY: ${{ secrets.FLASK_SECRET_KEY }}
```

No secret value is stored in the repository or README.

### GitHub Actions evidence

- `13-github-actions-build-test-success.png` — successful Build and Test workflow run on `staging`.
- `14-github-actions-staging-deployment-success.png` — successful staging deployment with the `staging-deployment` artifact.
- `15-github-actions-production-deployment-success.png` — successful production deployment triggered by the `v1.0.0` tag with the `production-deployment` artifact.

## CI/CD Evidence Index

| Screenshot | Evidence demonstrated |
|---|---|
| `01-tests-passed-after-mongodb-fix.png` | Local automated tests passing after the local MongoDB connection issue was resolved. |
| `02-jenkins-pipeline-configuration.png` | Jenkins Pipeline configured from the GitHub repository and `Jenkinsfile`. |
| `03-jenkins-build-test-output.png` | Jenkins dependency installation and passing pytest execution. |
| `04-jenkins-deployment-success.png` | Successful Jenkins staging deployment and pipeline completion. |
| `05-staging-deployment-files.png` | Application files present in the Jenkins staging directory. |
| `06-jenkins-github-trigger.png` | Jenkins GitHub hook trigger configuration. |
| `07-ngrok-jenkins-tunnel.png` | Temporary public tunnel used during webhook testing. |
| `08-github-webhook-configured.png` | GitHub webhook configured for Jenkins push events. |
| `09-jenkins-github-auto-trigger.png` | Jenkins automatically triggered by a GitHub push. |
| `10-jenkins-auto-build-success.png` | Automatically triggered Jenkins build completed successfully. |
| `11-jenkins-email-configuration.png` | Jenkins Gmail SMTP/email notification configuration. |
| `12-jenkins-pipeline-success-email.png` | Actual successful Jenkins pipeline email received. |
| `13-github-actions-build-test-success.png` | GitHub Actions Build and Test completed successfully on `staging`. |
| `14-github-actions-staging-deployment-success.png` | GitHub Actions staging deployment completed successfully. |
| `15-github-actions-production-deployment-success.png` | GitHub Actions production deployment completed successfully from tag `v1.0.0`. |

## Repository Structure

```text
flask_Practice/
├── .github/
│   └── workflows/
│       └── ci-cd.yml
├── screenshots/
├── templates/
├── .gitignore
├── app.py
├── Jenkinsfile
├── LICENSE
├── README.md
├── README.pdf
├── requirements.txt
├── start_flask.sh
├── test_app.py
└── azure-pipelines.yml
```

## Security Notes

- Never commit `.env`, passwords, app passwords, API keys, or other secrets.
- Store Jenkins SMTP credentials securely in Jenkins Credentials.
- Store GitHub Actions secrets in GitHub Secrets rather than source files.
- Keep temporary public tunnels such as ngrok disabled when they are not required.
- Use a dedicated SMTP credential rather than a normal Gmail account password.

## License

MIT License
