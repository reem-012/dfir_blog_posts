---
title: "Docker Compose Profiles and Environment Variables Guide"
date: "2025-04-18"
author: "reem"
tags: ["docker", "dev-ops"]
description: "Brief intoduction to docker compose profiles"
---

# Docker Compose Profiles and Environment Variables Guide

This document provides a comprehensive guide to using Docker Compose profiles and environment variables for managing different deployment configurations.

## Project Structure

The project has the following directory structure:

```
.
├── docker-compose.yml
├── .env
├── .env_test
├── prod
│   ├── app1
│   │   └── Dockerfile
│   └── app2
│       └── Dockerfile
└── test
    ├── app1
    │   └── Dockerfile
    └── app2
        └── Dockerfile
```

## Dockerfile Contents

### Production Dockerfiles

**./prod/app1/Dockerfile**
```dockerfile
FROM alpine
ENV VAR1=default_value
CMD echo "Variable One: ${VAR1}"
```

**./prod/app2/Dockerfile**
```dockerfile
FROM alpine
ENV VAR2=default_value
CMD echo "Variable Two: ${VAR2}"
```

### Test Dockerfiles

**./test/app1/Dockerfile**
```dockerfile
FROM alpine
ENV VAR1=default_value
CMD echo "Variable One from test: ${VAR1}"
```

**./test/app2/Dockerfile**
```dockerfile
FROM alpine
ENV VAR2=default_value
CMD echo "Variable Two from test: ${VAR2}"
```

## Environment Files

**.env (Production)**
```
VAR1=Hello from App 1
VAR2=Hello from App 2
```

**.env_test (Testing)**
```
VAR1=TEST from App 1
VAR2=TEST from App 2
```

## Docker Compose Configuration

### Basic Docker Compose

**docker-compose.yml (Initial Version)**
```yaml
services:
  app1:
    build:
      context: ./prod/app1
    environment:
      - VAR1=${VAR1}
  app2:
    build:
      context: ./prod/app2
    environment:
      - VAR2=${VAR2}
```

### With Profiles Added

**docker-compose.yml (With Profiles)**
```yaml
services:
  app1:
    build:
      context: ./prod/app1
    environment:
      - VAR1=${VAR1}
    profiles:
      - prod
  app2:
    build:
      context: ./prod/app2
    environment:
      - VAR2=${VAR2}
    profiles:
      - prod
  app1-test:
    build:
      context: ./test/app1
    environment:
      - VAR1=${VAR1}
    profiles:
      - test
  app2-test:
    build:
      context: ./test/app2
    environment:
      - VAR2=${VAR2}
    profiles:
      - test
```

### Using YAML Extensions (Improved Version)

**docker-compose.yml (With YAML Extensions)**
```yaml
x-app1-env: &app1-env
  environment:
    - VAR1=${VAR1}
x-app2-env: &app2-env
  environment:
    - VAR2=${VAR2}
services:
  app1:
    build:
      context: ./prod/app1
    <<: *app1-env
    profiles:
      - prod
  app2:
    build:
      context: ./prod/app2
    <<: *app2-env
    profiles:
      - prod
  app1-test:
    build:
      context: ./test/app1
    <<: *app1-env
    profiles:
      - test
  app2-test:
    build:
      context: ./test/app2
    <<: *app2-env
    profiles:
      - test
```

## Running Commands

### Basic Run (No Profile)

```bash
docker compose up
```

**Expected Output**
```
app1-1 | Variable One: Hello from App 1
app2-1 | Variable Two: Hello from App 2
app1-1 exited with code 0
app2-1 exited with code 0
```

### Running with Production Profile

```bash
docker compose --profile prod up
```

**Expected Output**
```
app1-1 | Variable One: Hello from App 1
app2-1 | Variable Two: Hello from App 2
app1-1 exited with code 0
app2-1 exited with code 0
```

### Running with Test Profile

```bash
docker compose --profile test up
```

**Expected Output**
```
app2-test-1 | Variable Two from test: Hello from App 2
app1-test-1 | Variable One from test: Hello from App 1
app2-test-1 exited with code 0
app1-test-1 exited with code 0
```

### Running with Test Profile and Alternate Env File

```bash
docker compose --env-file .env_test --profile test up
```

**Expected Output**
```
app1-test-1 | Variable One from test: TEST from App 1
app2-test-1 | Variable Two from test: TEST from App 2
app1-test-1 exited with code 0
app2-test-1 exited with code 0
```

## Key Benefits

1. **Profiles** - Allow selective service startup based on environment (prod, test, etc.)
2. **Environment Variables** - Provide configuration without modifying Docker files
3. **Custom Env Files** - Enable different configurations for different environments
4. **YAML Extensions** - Reduce duplication in docker-compose.yml by using anchors and aliases

## Common Issues and Troubleshooting

1. Make sure the .env file is in the same directory as the docker-compose.yml file
2. Verify file permissions are correct for all files
3. Double-check that your profiles are correctly specified when running commands
4. Ensure environment variables are properly referenced in your Dockerfiles

## Advanced Usage

- You can combine multiple profiles: `docker compose --profile prod --profile monitoring up`
- You can override individual environment variables: `VAR1=override docker compose up`
- For more complex setups, consider using multiple compose files with `docker compose -f docker-compose.yml -f docker-compose.prod.yml up`
