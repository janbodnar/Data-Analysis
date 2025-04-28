Below is an updated version of the Docker tutorial for Python programmers, modified to introduce simple Python programs before diving into more complex examples like the Flask app. The tutorial now starts with basic Python scripts to ease beginners into Docker, then progresses to the Flask app and other advanced topics. The structure remains the same, with only the relevant sections updated to include these simpler examples.


# Docker Tutorial for Python Programmers

This tutorial is designed for Python programmers who want to learn how to use Docker to containerize their applications. We'll start with simple Python scripts, then move to more complex applications, covering Docker basics, containerization, and best practices.

## 1. Introduction to Docker

Docker is a platform that allows you to package applications and their dependencies into containers. Containers are lightweight, portable, and run consistently across different environments.

### Why Use Docker for Python?
- **Consistency**: Ensure your Python app runs the same way in development, testing, and production.
- **Dependency Management**: Isolate Python libraries and dependencies to avoid conflicts.
- **Scalability**: Easily deploy and scale Python apps in cloud environments.
- **Portability**: Share your app with others without worrying about environment setup.

## 2. Installing Docker

To follow this tutorial, install Docker Desktop (for macOS/Windows) or Docker Engine (for Linux). Visit [docker.com](https://www.docker.com/get-started) for instructions.

Verify installation:
```bash
docker --version
docker run hello-world
```

## 3. Docker Basics

- **Image**: A blueprint for creating containers (e.g., a Python 3.9 image).
- **Container**: A running instance of an image.
- **Dockerfile**: A script to define how an image is built.
- **Docker Hub**: A registry to store and share Docker images.

### Key Commands
```bash
docker pull python:3.9  # Download Python 3.9 image
docker images           # List local images
docker run -it python:3.9 bash  # Start a container and open a shell
docker ps               # List running containers
docker stop <container_id>  # Stop a container
```

```bash
## Commands

`$ docker build -t myapp .` - build image from Dockerfile  
`$ docker tag myapp janbodnar/spring-boot-simple:first` - tag image  
`$ docker ps` - show running containers  
`$ docker image ls` - list Docker images  
`$ docker images` - list Docker images  
`$ docker pull devilbox/php-fpm-7.4` - pull image  
`$ docker image rm -f a084fe86889b` - remove image 
`$ docker rmi -f 90b1c3e39075`  - remove image  
`$ docker run -it --entrypoint /bin/bash <image id>` run shell in image  
`$ docker exec -it <container name> /bin/bash` - connect into container  
`$ docker cp hello.php infallible_bassi:/var/www/html` - copy file to container  
`$ docker run -p 127.0.0.1:80:8080/tcp ubuntu bash` - binds port 8080 of the container to 
TCP port 80 on 127.0.0.1 of the host machine  
`$ docker stop 03ccf5a72537` - stop running container  
`$ docker push janbodnar/spring-boot-simple:first`  - upload image to hub  
`$ docker pull janbodnar/spring-boot-simple:first`  - pull image from hub  

`$ docker run -it python:3.8 hostname -i`  -- find out IP address  

`# systemctl start docker` -- start docker  
`sudo systemctl enable docker` -- enable docker to star on boot  
`sudo usermod -aG docker $USER`  -- allow non-root user to run docker  

**prune dangling &lt;none&gt;:&lt;none&gt; images**  
`# docker image prune`  
`# docker rmi -f $(docker images -f "dangling=true" -q)`
```



## 4. Containerizing Simple Python Programs

Let’s start by containerizing two simple Python programs to understand Docker basics.

### Example 1: Hello World Script

Create a directory `hello-python` with the following file:

**hello.py**
```python
print("Hello, Docker from Python!")
```

**Dockerfile**
```dockerfile
# Use official Python 3.9 slim image
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy the script
COPY hello.py .

# Command to run the script
CMD ["python", "hello.py"]
```

**Build and Run**:
```bash
# Build the image
docker build -t hello-python .

# Run the container
docker run hello-python
```

**Expected Output**:
```
Hello, Docker from Python!
```

### Example 2: Simple Calculator Script

Now, let’s containerize a script that performs basic calculations.

Create a directory `calc-python` with the following files:

**calculator.py**
```python
def add(a, b):
    return a + b

result = add(5, 3)
print(f"5 + 3 = {result}")
```

**Dockerfile**
```dockerfile
# Use official Python 3.9 slim image
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy the script
COPY calculator.py .

# Command to run the script
CMD ["python", "calculator.py"]
```

**Build and Run**:
```bash
# Build the image
docker build -t calc-python .

# Run the container
docker run calc-python
```

**Expected Output**:
```
5 + 3 = 8
```

These examples demonstrate how to containerize basic Python scripts with minimal setup.

## 5. Containerizing a Python Web Application

Now, let’s move to a more complex example: a Flask web application.

### Step 1: Create a Flask App
Create a directory `my-python-app` with the following files:

**app.py**
```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello, Dockerized Python App!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

**requirements.txt**
```
flask==2.0.1
```

### Step 2: Create a Dockerfile
```dockerfile
# Use official Python 3.9 image as base
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy requirements file and install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy the application code
COPY app.py .

# Expose port 5000
EXPOSE 5000

# Command to run the app
CMD ["python", "app.py"]
```

### Step 3: Build and Run
```bash
# Build the image
docker build -t my-python-app .

# Run the container
docker run -p 5000:5000 my-python-app
```

Visit `http://localhost:5000` in your browser to see the app running.

## 6. Best Practices for Python Docker Images

### Use Lightweight Base Images
- Use `python:3.9-slim` instead of `python:3.9` to reduce image size.
- For even smaller images, consider `python:3.9-alpine` (but note potential compatibility issues).

### Minimize Layers
- Combine commands to reduce layers:
```dockerfile
RUN pip install --no-cache-dir -r requirements.txt && rm -rf /root/.cache
```

### Use .dockerignore
Create a `.dockerignore` file:
```
.git
__pycache__
*.pyc
.env
```

### Pin Dependencies
In `requirements.txt`, specify exact versions:
```
flask==2.0.1
werkzeug==2.0.1
```

### Run as Non-Root User
```dockerfile
# Create a non-root user
RUN useradd -m myuser
USER myuser
```

## 7. Working with Docker Compose

For multi-container apps (e.g., Python app + database), use Docker Compose.

### Example: Flask App with PostgreSQL
Create a `docker-compose.yml`:

```yaml
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
```

Run the stack:
```bash
docker-compose up
```

## 8. Debugging and Troubleshooting

- **View Logs**: `docker logs <container_id>`
- **Access Shell**: `docker exec -it <container_id> bash`
- **Check Resources**: `docker stats`
- **Image Size**: Use `docker images` to optimize large images.

## 9. Deploying to Production

- **Push to Docker Hub**:
```bash
docker tag my-python-app username/my-python-app:latest
docker push username/my-python-app:latest
```

- **Use Cloud Platforms**: Deploy to AWS ECS, Google Cloud Run, or Kubernetes.
- **Environment Variables**:
```bash
docker run -e API_KEY=your_key -p 5000:5000 my-python-app
```

## 10. Exercise for Attendees

**Task**: Containerize a Python script that fetches data from an API and saves it to a file.

1. Create a script using `requests` to fetch data from `https://jsonplaceholder.typicode.com/posts`.
2. Write a Dockerfile to containerize it.
3. Build and run, ensuring the output file is accessible.

**Solution**:

**fetch_data.py**
```python
import requests

response = requests.get("https://jsonplaceholder.typicode.com/posts")
with open("output.json", "w") as f:
    f.write(response.text)
print("Data saved to output.json")
```

**Dockerfile**
```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY fetch_data.py .
CMD ["python", "fetch_data.py"]
```

**requirements.txt**
```
requests==2.26.0
```

**Run**:
```bash
docker build -t fetch-data .
docker run -v $(pwd)/output:/app/output fetch-data
```

The output will be saved to `output/output.json`.

## 11. Additional Resources
- [Docker Documentation](https://docs.docker.com)
- [Docker Hub Python Images](https://hub.docker.com/_/python)
- [Docker Compose Reference](https://docs.docker.com/compose)
- [Best Practices for Python Docker](https://pythonspeed.com/docker)

---

This tutorial starts with simple Python programs to build confidence before tackling complex applications. Encourage attendees to try the examples and ask questions!
