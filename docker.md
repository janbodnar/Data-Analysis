Docker Tutorial for Python Programmers
This tutorial is designed for Python programmers who want to learn how to use Docker to containerize their applications. We'll cover the basics of Docker, how to create Docker images for Python apps, and best practices to ensure efficient and secure deployments.
1. Introduction to Docker
Docker is a platform that allows you to package applications and their dependencies into containers. Containers are lightweight, portable, and run consistently across different environments.
Why Use Docker for Python?

Consistency: Ensure your Python app runs the same way in development, testing, and production.
Dependency Management: Isolate Python libraries and dependencies to avoid conflicts.
Scalability: Easily deploy and scale Python apps in cloud environments.
Portability: Share your app with others without worrying about environment setup.

2. Installing Docker
To follow this tutorial, install Docker Desktop (for macOS/Windows) or Docker Engine (for Linux). Visit docker.com for instructions.
Verify installation:
docker --version
docker run hello-world

3. Docker Basics

Image: A blueprint for creating containers (e.g., a Python 3.9 image).
Container: A running instance of an image.
Dockerfile: A script to define how an image is built.
Docker Hub: A registry to store and share Docker images.

Key Commands
docker pull python:3.9  # Download Python 3.9 image
docker images           # List local images
docker run -it python:3.9 bash  # Start a container and open a shell
docker ps               # List running containers
docker stop <container_id>  # Stop a container

4. Containerizing a Simple Python Application
Let’s create a Python app and containerize it using Docker.
Step 1: Create a Python App
Create a directory my-python-app with the following files:
app.py
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello, Dockerized Python App!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)

requirements.txt
flask==2.0.1

Step 2: Create a Dockerfile
In the same directory, create a Dockerfile:
# Use official Python 3.9 image as base
FROM python:3.9-slim

# Set working directory inside the container
WORKDIR /app

# Copy requirements file and install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy the application code
COPY app.py .

# Expose port 5000 for the Flask app
EXPOSE 5000

# Command to run the app
CMD ["python", "app.py"]

Step 3: Build and Run the Docker Image
# Build the image
docker build -t my-python-app .

# Run the container
docker run -p 5000:5000 my-python-app

Visit http://localhost:5000 in your browser to see the app running.
5. Best Practices for Python Docker Images
Use Lightweight Base Images

Use python:3.9-slim instead of python:3.9 to reduce image size.
For even smaller images, consider python:3.9-alpine (but note potential compatibility issues with some packages).

Minimize Layers

Combine commands in the Dockerfile to reduce layers:

RUN pip install --no-cache-dir -r requirements.txt && rm -rf /root/.cache

Use .dockerignore
Create a .dockerignore file to exclude unnecessary files (e.g., .git, __pycache__):
.git
__pycache__
*.pyc
.env

Pin Dependencies
In requirements.txt, specify exact versions to avoid unexpected updates:
flask==2.0.1
werkzeug==2.0.1

Run as Non-Root User
For security, run the container as a non-root user:
# Create a non-root user
RUN useradd -m myuser
USER myuser

6. Working with Docker Compose
For multi-container apps (e.g., Python app + database), use Docker Compose.
Example: Flask App with PostgreSQL
Create a docker-compose.yml:
version: '3.8'
services:
  web:
    build: .
    ports:
      - "5000:5000"
    depends_on:
      - db
  db:
    image: postgres:13
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
      POSTGRES_DB: mydb

Run the stack:
docker-compose up

7. Debugging and Troubleshooting

View Logs: docker logs <container_id>
Access Container Shell: docker exec -it <container_id> bash
Check Resource Usage: docker stats
Image Size Issues: Use docker images to identify large images and optimize your Dockerfile.

8. Deploying to Production

Push to Docker Hub:

docker tag my-python-app username/my-python-app:latest
docker push username/my-python-app:latest


Use Cloud Platforms: Deploy to AWS ECS, Google Cloud Run, or Kubernetes.
Environment Variables: Pass sensitive data (e.g., API keys) via environment variables:

docker run -e API_KEY=your_key -p 5000:5000 my-python-app

9. Exercise for Attendees
Task: Containerize a Python script that fetches data from an API and saves it to a file.

Create a Python script using requests to fetch data from https://jsonplaceholder.typicode.com/posts.
Write a Dockerfile to containerize the script.
Build and run the container, ensuring the output file is accessible.

Solution (for reference):
fetch_data.py
import requests

response = requests.get("https://jsonplaceholder.typicode.com/posts")
with open("output.json", "w") as f:
    f.write(response.text)
print("Data saved to output.json")

Dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY fetch_data.py .
CMD ["python", "fetch_data.py"]

requirements.txt
requests==2.26.0

Run:
docker build -t fetch-data .
docker run -v $(pwd)/output:/app/output fetch-data

The output will be saved to output/output.json on the host.
10. Additional Resources

Docker Documentation
Docker Hub Python Images
Docker Compose Reference
Best Practices for Python Docker

