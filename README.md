# Sample Python Application with Docker and Jenkins Pipeline

This project demonstrates a simple Python application containerized with Docker and automated CI/CD using Jenkins Pipeline.

## 🚀 Features

- Python-based web application
- Docker containerization
- Automated CI/CD with Jenkins Pipeline
- Unit testing integration
- Docker image optimization

## 📋 Prerequisites

- Python 3.8+
- Docker
- Jenkins
- Git

## 🛠️ Local Development Setup

1. Clone the repository:
```bash
git clone https://github.com/yourusername/your-repo-name.git
cd your-repo-name
```

2. Create and activate virtual environment:
```bash
python -m venv venv
source venv/bin/activate  # On Windows: .\venv\Scripts\activate
```

3. Install dependencies:
```bash
pip install -r requirements.txt
```

4. Run the application locally:
```bash
python app/app.py
```

## 🐳 Docker Setup

1. Build the Docker image:
```bash
docker build -t python-app:latest .
```

2. Run the container:
```bash
docker run -d -p 5000:5000 python-app:latest
```

## 🔄 Jenkins Pipeline

The project includes a Jenkinsfile that defines the CI/CD pipeline with the following stages:

1. Checkout
2. Build Docker Image
3. Run Tests
4. Deploy

### Jenkins Pipeline Setup

1. Create a new Pipeline job in Jenkins
2. Configure Git repository in Jenkins
3. Point to the Jenkinsfile in the repository
4. Configure necessary credentials:
   - Docker registry credentials
   - Deployment credentials

### Jenkins Pipeline Details

- **Environment Variables**:
  - `DOCKER_IMAGE`: Name of the Docker image.
  - `DOCKER_TAG`: Tag for the Docker image, set to the Jenkins build number.
  - `DOCKER_FULL_IMAGE`: Full image name with tag.

- **Stages**:
  - **Checkout**: Cleans workspace and checks out the source code.
  - **Build Docker Image**: Builds the Docker image using the `Dockerfile`.
  - **Run Tests**: Executes tests inside the Docker container.
  - **Deploy**: Stops and removes any existing container and runs a new one with the latest image.

- **Post Actions**:
  - On failure, stops and removes any running containers to clean up.

## 📁 Project Structure

```
.
├── app/
│   ├── __init__.py
│   └── main.py
├── tests/
│   └── test_main.py
├── Dockerfile
├── Jenkinsfile
├── requirements.txt
└── README.md
```

## 🧪 Running Tests

```bash
pytest tests/
```

## 🚀 Deployment

The application can be deployed using:

```bash
docker-compose up -d
```
