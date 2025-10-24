# Local Docker Image Compilation Method

This project now uses GitHub's automatic Docker compilation functionality. This document is prepared for friends who need to compile Docker images locally.

## Prerequisites

1. **Install Docker**
```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

2. **Verify Docker Installation**
```bash
docker --version
docker-compose --version
```

## Compilation Process

### 1. Navigate to Project Root Directory

```bash
cd /path/to/xiaozhi-esp32-server
```

### 2. Compile Server Image

```bash
# Compile server image
docker build -t xiaozhi-esp32-server:server_latest -f ./Dockerfile-server .
```

### 3. Compile Web Image

```bash
# Compile web image
docker build -t xiaozhi-esp32-server:web_latest -f ./Dockerfile-web .
```

### 4. Compile Base Image (Optional)

```bash
# Compile base image
docker build -t xiaozhi-esp32-server:base_latest -f ./Dockerfile-server-base .
```

## Deployment

### 1. Update Docker Compose Configuration

After compilation, you need to modify `docker-compose.yml` to use your locally compiled image versions:

```yaml
version: '3.8'

services:
  xiaozhi-server:
    image: xiaozhi-esp32-server:server_latest
    # ... other configurations

  xiaozhi-web:
    image: xiaozhi-esp32-server:web_latest
    # ... other configurations
```

### 2. Start Services

```bash
# Navigate to xiaozhi-server directory
cd main/xiaozhi-server

# Start services
docker-compose up -d
```

### 3. Verify Deployment

```bash
# Check running containers
docker-compose ps

# View logs
docker-compose logs -f
```

## Advanced Compilation Options

### Multi-Architecture Builds

```bash
# Build for multiple architectures
docker buildx build --platform linux/amd64,linux/arm64 -t xiaozhi-esp32-server:server_latest -f ./Dockerfile-server .
```

### Build with Custom Tags

```bash
# Build with version tags
docker build -t xiaozhi-esp32-server:server_v1.0.0 -f ./Dockerfile-server .
docker build -t xiaozhi-esp32-server:web_v1.0.0 -f ./Dockerfile-web .
```

### Build with Build Arguments

```bash
# Build with custom arguments
docker build --build-arg PYTHON_VERSION=3.9 --build-arg NODE_VERSION=18 -t xiaozhi-esp32-server:server_latest -f ./Dockerfile-server .
```

## Dockerfile Optimization

### Server Dockerfile Optimization

```dockerfile
# Multi-stage build for smaller image size
FROM python:3.9-slim as builder

# Install build dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy requirements and install Python packages
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Final stage
FROM python:3.9-slim

# Copy installed packages from builder stage
COPY --from=builder /usr/local/lib/python3.9/site-packages /usr/local/lib/python3.9/site-packages

# Copy application code
COPY . /app
WORKDIR /app

# Create non-root user
RUN useradd -m -u 1000 xiaozhi
USER xiaozhi

# Expose ports
EXPOSE 8000 8003

# Start application
CMD ["python", "app.py"]
```

### Web Dockerfile Optimization

```dockerfile
# Multi-stage build for Node.js application
FROM node:18-alpine as builder

# Set working directory
WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm ci --only=production

# Copy source code
COPY . .

# Build application
RUN npm run build

# Production stage
FROM nginx:alpine

# Copy built application
COPY --from=builder /app/dist /usr/share/nginx/html

# Copy nginx configuration
COPY nginx.conf /etc/nginx/nginx.conf

# Expose port
EXPOSE 80

# Start nginx
CMD ["nginx", "-g", "daemon off;"]
```

## Build Scripts

### Automated Build Script

Create a `build.sh` script for automated compilation:

```bash
#!/bin/bash

# Build script for xiaozhi-esp32-server Docker images

set -e

# Configuration
PROJECT_NAME="xiaozhi-esp32-server"
VERSION=${1:-"latest"}
REGISTRY=${2:-"localhost:5000"}

echo "Building Docker images for $PROJECT_NAME version $VERSION"

# Build server image
echo "Building server image..."
docker build -t $PROJECT_NAME:server_$VERSION -f ./Dockerfile-server .

# Build web image
echo "Building web image..."
docker build -t $PROJECT_NAME:web_$VERSION -f ./Dockerfile-web .

# Build base image
echo "Building base image..."
docker build -t $PROJECT_NAME:base_$VERSION -f ./Dockerfile-server-base .

# Tag images for registry
echo "Tagging images for registry..."
docker tag $PROJECT_NAME:server_$VERSION $REGISTRY/$PROJECT_NAME:server_$VERSION
docker tag $PROJECT_NAME:web_$VERSION $REGISTRY/$PROJECT_NAME:web_$VERSION
docker tag $PROJECT_NAME:base_$VERSION $REGISTRY/$PROJECT_NAME:base_$VERSION

echo "Build completed successfully!"
echo "Images:"
echo "  - $PROJECT_NAME:server_$VERSION"
echo "  - $PROJECT_NAME:web_$VERSION"
echo "  - $PROJECT_NAME:base_$VERSION"
```

### Usage

```bash
# Make script executable
chmod +x build.sh

# Build with default version
./build.sh

# Build with custom version
./build.sh v1.0.0

# Build and tag for registry
./build.sh v1.0.0 myregistry.com:5000
```

## Registry Management

### Push to Registry

```bash
# Push images to registry
docker push localhost:5000/xiaozhi-esp32-server:server_latest
docker push localhost:5000/xiaozhi-esp32-server:web_latest
docker push localhost:5000/xiaozhi-esp32-server:base_latest
```

### Pull from Registry

```bash
# Pull images from registry
docker pull localhost:5000/xiaozhi-esp32-server:server_latest
docker pull localhost:5000/xiaozhi-esp32-server:web_latest
docker pull localhost:5000/xiaozhi-esp32-server:base_latest
```

## Troubleshooting

### Common Build Issues

1. **Build Context Too Large**
   ```bash
   # Use .dockerignore to exclude unnecessary files
   echo "node_modules" >> .dockerignore
   echo "*.log" >> .dockerignore
   echo ".git" >> .dockerignore
   ```

2. **Memory Issues**
   ```bash
   # Increase Docker memory limit
   docker build --memory=4g -t xiaozhi-esp32-server:server_latest -f ./Dockerfile-server .
   ```

3. **Network Issues**
   ```bash
   # Use custom DNS
   docker build --dns=8.8.8.8 -t xiaozhi-esp32-server:server_latest -f ./Dockerfile-server .
   ```

### Build Optimization

1. **Layer Caching**
   ```dockerfile
   # Order layers from least to most frequently changing
   COPY requirements.txt .
   RUN pip install -r requirements.txt
   COPY . .
   ```

2. **Multi-stage Builds**
   ```dockerfile
   # Use multi-stage builds to reduce final image size
   FROM node:18-alpine as builder
   # ... build steps
   FROM nginx:alpine
   COPY --from=builder /app/dist /usr/share/nginx/html
   ```

3. **Base Image Optimization**
   ```dockerfile
   # Use specific base image versions
   FROM python:3.9-slim
   # Instead of
   FROM python:latest
   ```

## CI/CD Integration

### GitHub Actions Example

```yaml
name: Build Docker Images

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v2
    
    - name: Build server image
      run: |
        docker build -t xiaozhi-esp32-server:server_latest -f ./Dockerfile-server .
    
    - name: Build web image
      run: |
        docker build -t xiaozhi-esp32-server:web_latest -f ./Dockerfile-web .
    
    - name: Push images
      run: |
        docker push ${{ secrets.REGISTRY_URL }}/xiaozhi-esp32-server:server_latest
        docker push ${{ secrets.REGISTRY_URL }}/xiaozhi-esp32-server:web_latest
```

## Security Considerations

### Image Security

1. **Use Official Base Images**
   ```dockerfile
   FROM python:3.9-slim
   # Instead of custom base images
   ```

2. **Regular Updates**
   ```dockerfile
   # Pin specific versions
   FROM python:3.9.7-slim
   ```

3. **Minimal Attack Surface**
   ```dockerfile
   # Remove unnecessary packages
   RUN apt-get update && apt-get install -y --no-install-recommends \
       python3-dev \
       && rm -rf /var/lib/apt/lists/*
   ```

### Registry Security

1. **Use Private Registries**
   ```bash
   # Use private registry for production
   docker tag xiaozhi-esp32-server:server_latest myregistry.com/xiaozhi-esp32-server:server_latest
   ```

2. **Image Scanning**
   ```bash
   # Scan images for vulnerabilities
   docker scan xiaozhi-esp32-server:server_latest
   ```

**Note: This Docker compilation guide may be outdated. Please refer to the latest Docker documentation and project-specific build requirements for current compilation procedures.**

## Additional Resources

- [Docker Documentation](https://docs.docker.com/)
- [Dockerfile Best Practices](https://docs.docker.com/develop/dev-best-practices/)
- [Multi-stage Builds](https://docs.docker.com/develop/dev-best-practices/dockerfile_best-practices/#use-multi-stage-builds)
- [Docker Security](https://docs.docker.com/engine/security/)
