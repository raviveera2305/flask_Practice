# Student Registration System

A lightweight Flask web application for managing student records with MongoDB. The application supports creating, viewing, updating, and deleting student records through a simple Bootstrap-based web interface.

## Features

- View all registered students
- Add new student records
- Update existing student records
- Delete student records
- Delete confirmation flow
- Responsive UI with Bootstrap 5
- MongoDB persistence through Flask-PyMongo
- Environment-based configuration using `.env`

## Technology Stack

| Layer | Technology |
|---|---|
| Backend | Python, Flask |
| Database | MongoDB, Flask-PyMongo, PyMongo |
| Frontend | HTML, Jinja2, Bootstrap 5 |
| Configuration | python-dotenv, `.env` |
| Testing | pytest |
| CI/CD | Jenkins Pipeline, GitHub Webhooks |

## Application Setup

### Prerequisites

- Python 3.x
- MongoDB running locally or a reachable MongoDB instance
- Git

### 1. Clone the repository

```bash
git clone https://github.com/raviveera2305/flask_Practice.git
cd flask_Practice
```

### 2. Create and activate a virtual environment

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

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure environment variables

Create a `.env` file in the project root:

```text
MONGO_URI=mongodb://localhost:27017/student_db
SECRET_KEY=your-secret-key
```

Do not commit `.env` or real credentials to source control.

### 5. Run the application

```bash
python app.py
```

The application runs on:

```text
http://localhost:5000
```

## Testing

Run the automated test suite with:

```bash
pytest -v
```

The test suite covers the main application operations, including viewing the home page and adding, updating, and deleting student records.

## Project Structure

```text
flask_Practice/
├── .github/
├── screenshots/
├── templates/
│   ├── base.html
│   ├── index.html
│   ├── add_student.html
│   └── update_student.html
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

## Jenkins CI/CD Pipeline

The project includes a Jenkins declarative pipeline that automates dependency installation, testing, and deployment to a staging directory.

### Pipeline flow

```text
GitHub push to main
        |
        v
GitHub Webhook
        |
        v
Jenkins Pipeline
   |       |       |
 Build    Test   Deploy
   |       |       |
   v       v       v
Install  pytest  Staging
packages  suite  deployment
        |
        v
Success / Failure email
```

### Pipeline stages

1. **Build** — installs Python dependencies from `requirements.txt`.
2. **Test** — runs the automated pytest suite.
3. **Deploy** — copies the application files to the Jenkins staging directory while excluding Git metadata and local screenshots.
4. **Post-build notification** — sends an email for successful or failed pipeline executions.

### Jenkins configuration

The Jenkins job is configured as a Pipeline using the repository's `Jenkinsfile` and the `main` branch.

The pipeline is triggered automatically by GitHub push events through the Jenkins GitHub webhook integration.

For the demonstrated Windows Jenkins environment, the staging deployment directory is:

```text
C:\Jenkins-Staging\flask_Practice
```

### Email notification

Jenkins is configured with Gmail SMTP using TLS on port `587`. The pipeline uses Jenkins' standard `mail` step for success and failure notifications.

SMTP credentials are stored in Jenkins credentials rather than in the repository.

## Jenkins Evidence

The following screenshots document the completed CI/CD implementation. They are stored in the `screenshots/` directory and are referenced here for traceability.

| Screenshot | Evidence | Reference |
|---|---|---|
| `01-tests-passed-after-mongodb-fix.png` | Local pytest validation after resolving the local MongoDB connection issue. | [View evidence](screenshots/01-tests-passed-after-mongodb-fix.png) |
| `02-jenkins-pipeline-configuration.png` | Jenkins Pipeline job configured from the GitHub repository and `Jenkinsfile`. | [View evidence](screenshots/02-jenkins-pipeline-configuration.png) |
| `03-jenkins-build-test-output.png` | Jenkins Build and Test stages, including the passing pytest suite. | [View evidence](screenshots/03-jenkins-build-test-output.png) |
| `04-jenkins-deployment-success.png` | Successful Jenkins staging deployment and completed pipeline. | [View evidence](screenshots/04-jenkins-deployment-success.png) |
| `05-staging-deployment-files.png` | Application files present in the Jenkins staging directory. | [View evidence](screenshots/05-staging-deployment-files.png) |
| `06-jenkins-github-trigger.png` | Jenkins GitHub hook trigger configuration. | [View evidence](screenshots/06-jenkins-github-trigger.png) |
| `07-ngrok-jenkins-tunnel.png` | Temporary public tunnel used to expose the local Jenkins webhook endpoint for testing. | [View evidence](screenshots/07-ngrok-jenkins-tunnel.png) |
| `08-github-webhook-configured.png` | GitHub webhook configured to deliver push events to Jenkins. | [View evidence](screenshots/08-github-webhook-configured.png) |
| `09-jenkins-github-auto-trigger.png` | Jenkins automatically triggered by a GitHub push. | [View evidence](screenshots/09-jenkins-github-auto-trigger.png) |
| `10-jenkins-auto-build-success.png` | Automatically triggered Jenkins build completed successfully. | [View evidence](screenshots/10-jenkins-auto-build-success.png) |
| `11-jenkins-email-configuration.png` | Jenkins email notification configuration, including Gmail SMTP settings. | [View evidence](screenshots/11-jenkins-email-configuration.png) |
| `12-jenkins-pipeline-success-email.png` | Actual Jenkins pipeline success notification received by email. | [View evidence](screenshots/12-jenkins-pipeline-success-email.png) |

## Security and Configuration Notes

- Keep `.env` out of source control.
- Store SMTP passwords, API keys, and other credentials in Jenkins Credentials or GitHub Secrets rather than in source files.
- The ngrok tunnel shown in the evidence was used only for webhook testing. It should not be left running unnecessarily because it exposes the local Jenkins service through a public endpoint.
- Ensure MongoDB is running and accessible through the configured `MONGO_URI` before starting the application or running integration tests.

## License

MIT License
