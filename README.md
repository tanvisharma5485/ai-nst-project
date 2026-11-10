# Neural Style Transfer (NST) with Adaptive Instance Normalization (AdaIN)

An end-to-end Deep Learning application that uses Adaptive Instance Normalization (AdaIN) to transfer artistic styles onto content images. The project combines PyTorch-based neural style transfer with a Flask web application and a Docker-based deployment pipeline using GitHub Actions, Docker Hub, and AWS EC2.

## Project Overview

This project implements arbitrary image style transfer using a pretrained VGG encoder and a custom-trained decoder network.

Users can upload a content image and a reference style image, adjust the stylization intensity using an alpha parameter, and generate an artistic output image.

The application also includes a containerized deployment workflow with automated Docker image building and publishing through GitHub Actions.

## Key Features

- **Arbitrary Style Transfer:** Apply different artistic styles to content images using AdaIN.
- **Deep Learning Architecture:** Pretrained VGG encoder combined with a custom decoder network.
- **Custom Model Training:** Decoder trained using content reconstruction and style losses.
- **Adjustable Stylization:** Alpha parameter controls the intensity of style transfer.
- **Web Interface:** Flask-based application with image upload functionality.
- **GPU Acceleration:** PyTorch CUDA support for accelerated inference.
- **Docker Containerization:** Application packaged into a Docker image.
- **Automated CI/CD:** GitHub Actions workflow for automated validation, image building, and publishing.
- **Docker Hub Integration:** Automatically publishes Docker images with `latest` and commit-specific tags.
- **AWS EC2 Deployment:** Runs the containerized application on an EC2 instance using SSH-based remote access.

## Technology Stack

| Category | Technologies |
|---|---|
| Programming Language | Python 3.11 |
| Deep Learning | PyTorch, Torchvision |
| Architecture | AdaIN, VGG19, Custom Decoder |
| GPU Acceleration | CUDA 12.8 |
| Backend | Flask |
| Frontend | Flask-Bootstrap, Flask-WTF, WTForms |
| Image Processing | Pillow |
| Production Server | Gunicorn |
| Containerization | Docker |
| CI/CD | GitHub Actions |
| Container Registry | Docker Hub |
| Cloud Deployment | AWS EC2 |
| Remote Access | SSH |

## Model Architecture

The application follows an encoder-decoder architecture based on Adaptive Instance Normalization.

1. **Content Encoder:** A pretrained VGG encoder extracts content features from the input image.
2. **Style Encoding:** Features from the style image provide style statistics.
3. **Adaptive Instance Normalization:** Aligns the channel-wise mean and standard deviation of content features with style features.
4. **Alpha Blending:** Controls the degree of stylization by interpolating between original and normalized features.
5. **Decoder:** A custom-trained decoder reconstructs the final stylized image.

## Model Training

The decoder network was trained from scratch using content and style image datasets.

Training components:

- Content reconstruction loss.
- Style loss based on feature mean and standard deviation.
- Adam optimizer.
- Learning-rate decay scheduling.
- Training progress monitoring using tqdm.

Pretrained model checkpoints:

- `vgg_normalised.pth`
- `decoder_final.pth`

## Web Application

The Flask application provides an interactive interface where users can:

1. Upload a content image.
2. Upload a reference style image.
3. Adjust the alpha parameter to control stylization strength.
4. Generate and view the stylized output.

Image preprocessing and postprocessing are handled using Pillow and Torchvision transforms.

## Docker Containerization

The application is containerized using a custom Dockerfile.

Key configurations:

- Python 3.11 slim base image.
- Dependency installation through `requirements.txt`.
- Installation of required system libraries.
- Application packaging inside the `/app` working directory.
- Port 5000 exposed for application access.
- Gunicorn configured as the production WSGI server.
- Single-worker configuration with a 300-second request timeout.

### Build Docker Image

```bash
docker build -t ai-nst-project .
```

### Run Docker Container

```bash
docker run -d \
  --name ai-nst-project \
  -p 5000:5000 \
  ai-nst-project
```

### Access Application

```text
http://localhost:5000
```

## CI/CD Pipeline – GitHub Actions

The project uses GitHub Actions to automate validation, Docker image building, and publishing.

The workflow is triggered by:

- Push events to the `main` branch.
- Pull requests targeting the `main` branch.

### Pipeline Stages

**1. Automated Python Checks**

- Sets up Python 3.11.
- Installs project dependencies.
- Validates Python syntax using `compileall`.
- Checks the existence of the trained decoder checkpoint.

**2. Docker Image Building**

- Configures Docker Buildx.
- Authenticates with Docker Hub using GitHub Secrets.
- Builds the Docker image using the project Dockerfile.

**3. Automated Docker Hub Publishing**

After successful checks, images are published to Docker Hub with two tags:

```text
tanvikumari03/ai-nst-project:latest

tanvikumari03/ai-nst-project:<commit-sha>
```

Commit-specific tags provide traceability between published Docker images and their corresponding source code revisions.

### CI/CD Workflow

```text
Developer Pushes Code to GitHub
              |
              v
       GitHub Actions
              |
              v
      Automated Python Checks
              |
              v
       Docker Buildx Setup
              |
              v
       Build Docker Image
              |
              v
       Docker Hub Login
              |
              v
     Push Image to Docker Hub
        (latest + commit SHA)
              |
              v
       AWS EC2 Deployment
       (SSH-based access)
              |
              v
       Pull Updated Image
              |
              v
       Run Docker Container
```

**Note:** The supplied GitHub Actions workflow automates Python checks and Docker Hub publishing. EC2 access and image deployment are handled through the SSH-based deployment process; the supplied YAML does not contain an automated SSH deployment job.

## AWS EC2 Deployment

The containerized application is deployed on an AWS EC2 instance.

Deployment process:

1. Connect to the EC2 instance using SSH.
2. Pull the latest application image from Docker Hub.
3. Run the updated Docker container.
4. Expose the application through the configured EC2 port and security group.

Example Docker image pull:

```bash
docker pull tanvikumari03/ai-nst-project:latest
```

The EC2 instance runs the application inside the Docker container using Gunicorn.

## Repository Structure

```text
ai-nst-project/
│
├── NST_Code/
│   ├── app.py
│   ├── experiment/
│   │   └── final_exp/
│   │       └── decoder_final.pth
│   └── ...
│
├── Demo_IO_Images/
│
├── .github/
│   └── workflows/
│       └── [CI/CD workflow]
│
├── Dockerfile
├── .dockerignore
├── Procfile
├── requirements.txt
└── README.md
```

## Running Locally

### 1. Clone Repository

```bash
git clone https://github.com/tanvisharma5485/ai-nst-project.git

cd ai-nst-project
```

### 2. Create Virtual Environment

```bash
python -m venv venv
```

Activate on Windows:

```powershell
venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Run Flask Application

```bash
python NST_Code/app.py
```

Open the application at:

```text
http://localhost:5000
```

## Docker Hub

Docker images are published automatically through GitHub Actions.

**Docker Hub Repository:**  
https://hub.docker.com/r/tanvikumari03/ai-nst-project

## GitHub Repository

https://github.com/tanvisharma5485/ai-nst-project

## Learning Outcomes

- Implemented an AdaIN-based neural style transfer architecture.
- Trained a custom decoder using deep learning optimization techniques.
- Integrated pretrained models for image generation.
- Developed and deployed a Flask-based ML application.
- Containerized a machine learning application using Docker.
- Implemented CI/CD automation using GitHub Actions.
- Automated Docker image publishing to Docker Hub.
- Deployed a containerized application on AWS EC2 using SSH.