# Dockerfile

**Dockerfile** is a text file containing instructions to build a Docker image. It automates the image creation process and ensures consistency across environments.

Documentation: [Dockerfile Reference](https://docs.docker.com/engine/reference/builder/)

---

## Basic Structure

A Dockerfile contains a series of instructions executed in order:

```dockerfile
# Comment
FROM base-image:tag
LABEL maintainer="email@example.com"
RUN command
COPY source destination
CMD ["executable", "param1", "param2"]
```

---

## Common Instructions

### FROM

Specifies the base image. Must be the first instruction (except for ARG).

```dockerfile
# Official image
FROM ubuntu:22.04

# Specific version
FROM node:18-alpine

# Multi-stage build
FROM node:18 AS builder
```

### RUN

Executes commands in a new layer and commits the results.

```dockerfile
# Shell form
RUN apt-get update && apt-get install -y nginx

# Exec form
RUN ["apt-get", "update"]

# Multiple commands (best practice - single layer)
RUN apt-get update && \
    apt-get install -y \
    nginx \
    curl \
    vim && \
    rm -rf /var/lib/apt/lists/*
```

### CMD

Provides defaults for executing container. Only one CMD instruction.

```dockerfile
# Exec form (preferred)
CMD ["nginx", "-g", "daemon off;"]

# Shell form
CMD nginx -g "daemon off;"

# As default parameters to ENTRYPOINT
CMD ["--help"]
```

### ENTRYPOINT

Configure container to run as executable.

```dockerfile
# Exec form
ENTRYPOINT ["python", "app.py"]

# Shell form
ENTRYPOINT python app.py

# Combined with CMD
ENTRYPOINT ["python", "app.py"]
CMD ["--port", "8000"]
```

**CMD vs ENTRYPOINT:**
- `CMD` can be overridden easily
- `ENTRYPOINT` defines the main command
- Use both: `ENTRYPOINT` for executable, `CMD` for default args

### COPY

Copies files/directories from build context to container.

```dockerfile
# Copy file
COPY app.py /app/

# Copy directory
COPY ./src /app/src

# Copy with pattern
COPY *.py /app/

# Copy with ownership
COPY --chown=user:group app.py /app/
```

### ADD

Similar to COPY but with extra features (auto-extract tar, remote URLs).

```dockerfile
# Copy and extract tar
ADD archive.tar.gz /app/

# Download from URL (not recommended)
ADD https://example.com/file.txt /app/

# Use COPY instead for local files
```

**Best Practice:** Use COPY instead of ADD unless you need tar extraction or URL download.

### WORKDIR

Sets working directory for subsequent instructions.

```dockerfile
WORKDIR /app

# Creates directory if it doesn't exist
WORKDIR /app/src

# Use absolute paths
WORKDIR /usr/local/app
```

### ENV

Sets environment variables.

```dockerfile
# Single variable
ENV NODE_ENV production

# Multiple variables
ENV NODE_ENV=production \
    PORT=8000 \
    DEBUG=false

# Used in subsequent instructions
ENV APP_HOME /app
WORKDIR $APP_HOME
```

### ARG

Defines build-time variables.

```dockerfile
# Define argument
ARG VERSION=latest
ARG BUILD_DATE

# Use in FROM (must be before FROM)
ARG BASE_IMAGE=node:18
FROM $BASE_IMAGE

# Use in other instructions
RUN echo "Building version ${VERSION}"
```

**Build with arguments:**
```bash
docker build --build-arg VERSION=1.2.3 .
```

### EXPOSE

Documents which ports the container listens on.

```dockerfile
# Single port
EXPOSE 80

# Multiple ports
EXPOSE 80 443

# UDP port
EXPOSE 53/udp

# Note: EXPOSE doesn't actually publish ports
# Use -p flag when running container
```

### VOLUME

Creates mount point for persistent data.

```dockerfile
# Single volume
VOLUME /data

# Multiple volumes
VOLUME ["/data", "/logs"]

# Named volume (preferred at runtime)
VOLUME /var/lib/mysql
```

### USER

Sets user for RUN, CMD, and ENTRYPOINT.

```dockerfile
# Create and switch to user
RUN useradd -m -u 1000 appuser
USER appuser

# Switch to root if needed
USER root

# User with group
USER appuser:appgroup
```

### LABEL

Adds metadata to image.

```dockerfile
LABEL version="1.0"
LABEL description="My application"
LABEL maintainer="email@example.com"

# Multiple labels
LABEL version="1.0" \
      description="My application" \
      maintainer="email@example.com"
```

### HEALTHCHECK

Defines health check command.

```dockerfile
# HTTP health check
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost/ || exit 1

# Custom health check
HEALTHCHECK --interval=5m --timeout=3s \
  CMD python healthcheck.py

# Disable inherited health check
HEALTHCHECK NONE
```

---

## Multi-Stage Builds

Build smaller, more secure images by using multiple FROM statements:

```dockerfile
# Stage 1: Build
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Production
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
CMD ["node", "dist/index.js"]
```

**Benefits:**
- Smaller final image (no build tools)
- Better security (fewer attack surfaces)
- Clear separation of concerns

---

## Best Practices

### 1. Use Specific Tags

```dockerfile
# ❌ Bad: uses latest
FROM node

# ✅ Good: specific version
FROM node:18-alpine
```

### 2. Minimize Layers

```dockerfile
# ❌ Bad: multiple RUN commands
RUN apt-get update
RUN apt-get install -y nginx
RUN apt-get install -y curl

# ✅ Good: combine commands
RUN apt-get update && \
    apt-get install -y \
    nginx \
    curl && \
    rm -rf /var/lib/apt/lists/*
```

### 3. Use .dockerignore

Create `.dockerignore` file:
```
node_modules
.git
.env
*.log
.DS_Store
```

### 4. Order Instructions by Change Frequency

```dockerfile
# Things that change rarely first
FROM node:18-alpine

# Dependencies (change occasionally)
COPY package*.json ./
RUN npm install

# Application code (changes frequently)
COPY . .

# This optimizes Docker layer caching
```

### 5. Don't Run as Root

```dockerfile
# Create non-root user
RUN addgroup -g 1000 appuser && \
    adduser -D -u 1000 -G appuser appuser

# Switch to user
USER appuser

# Application runs as appuser
CMD ["node", "app.js"]
```

---

## Example Dockerfiles

### Node.js Application

```dockerfile
FROM node:18-alpine AS builder

WORKDIR /app

# Install dependencies
COPY package*.json ./
RUN npm ci --only=production

# Copy application
COPY . .

# Production image
FROM node:18-alpine

WORKDIR /app

# Copy from builder
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist

# Create non-root user
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

USER nodejs

EXPOSE 3000

CMD ["node", "dist/index.js"]
```

### Python Application

```dockerfile
FROM python:3.11-slim AS builder

WORKDIR /app

# Install dependencies
COPY requirements.txt .
RUN pip install --user --no-cache-dir -r requirements.txt

# Production image
FROM python:3.11-slim

WORKDIR /app

# Copy dependencies from builder
COPY --from=builder /root/.local /root/.local

# Copy application
COPY . .

# Create non-root user
RUN useradd -m -u 1000 appuser && \
    chown -R appuser:appuser /app

USER appuser

EXPOSE 8000

CMD ["python", "app.py"]
```

---

## Related Topics

- [[docker/docker|Docker]] - Container platform
- [[docker/docker-compose|Docker Compose]] - Multi-container apps
- [[docker/docker-networking|Docker Networking]] - Container networking

---

## Quick Reference

```dockerfile
FROM image:tag              # Base image
RUN command                 # Execute command
COPY src dest              # Copy files
WORKDIR /path              # Set working directory
ENV KEY=value              # Set environment variable
EXPOSE port                # Document port
CMD ["executable"]         # Default command
ENTRYPOINT ["executable"]  # Main executable
USER username              # Set user
VOLUME /path               # Create volume mount
ARG name=default           # Build argument
LABEL key=value            # Add metadata
HEALTHCHECK CMD command    # Health check
```

---

## Security Best Practices

🔒 Don't store secrets in Dockerfile
🔒 Use specific image tags, not `latest`
🔒 Run as non-root user
🔒 Minimize attack surface (small images)
🔒 Scan images for vulnerabilities
🔒 Keep base images updated
🔒 Use trusted base images
🔒 Don't include unnecessary tools
🔒 Use `.dockerignore` to exclude sensitive files
🔒 Regularly rebuild images with security updates