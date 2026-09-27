## Goal

In this lab you will learn how to:

- build a Docker image
    
- understand the problem of unnecessary build dependencies
    
- create a multi-stage Dockerfile
    
- compare two Docker images
    
- run the optimized image
    

## Prerequisites

Before starting this lab, make sure the following software is installed:

- Docker Desktop
    
- Node.js 22 LTS
    
- A terminal such as PowerShell or Windows Terminal
    

### Install Node.js

Download and install **Node.js 22 LTS** for Windows from the official Node.js website:

[Node.js 22 LTS Downloads](https://nodejs.org/en/download/archive/v22.22.3?utm_source=chatgpt.com)

For most Windows computers, use the **Windows x64 Installer (.msi)**.

After the installation, open a new terminal and verify that Node.js and npm are available:

```
node --version
```

```
npm --version
```

Both commands should return a version number.

> **Note:** npm is installed automatically together with Node.js.

---

# 1. Project

You are given a very small web application. (EduMoodle)

Project structure:

```
multi-stage-lab/
│
├── src/
│   └── index.html
│
├── package.json
├── Dockerfile.single
└── Dockerfile
```

---

# 2. Application files

## `src/index.html`

```
<!DOCTYPE html>
<html>
<head>
    <title>Docker Multi-Stage Lab</title>
</head>

<body>
    <h1>Hello Docker!</h1>
    <p>This application was built using a multi-stage build.</p>
</body>
</html>
```

---

## `package.json`

```
{
  "scripts": {
    "build": "mkdir -p dist && cp src/index.html dist/index.html"
  }
}
```

The command:

```
npm run build
```

creates:

```
dist/
└── index.html
```

---

# 3. Build the application manually

Run:

```
npm run build
```

Check the result:

```
ls dist
```

You should see:

```
index.html
```

---

# 4. Single-Stage Dockerfile

Open:

```
Dockerfile.single
```

It contains:

```
FROM node:22

WORKDIR /app

COPY . .

RUN npm run build

RUN npm install -g serve

EXPOSE 3000

CMD ["serve", "-s", "dist", "-l", "3000"]
```

Build the image:

```
docker build \
  -f Dockerfile.single \
  -t webapp:single .
```

Check the image:

```
docker images
```

Write down the size:

```
webapp:single size: __________
```

---

# 5. Run the container

```
docker run \
  --rm \
  -p 8080:3000 \
  webapp:single
```

Open:

```
http://localhost:8080
```

You should see:

```
Hello Docker!
```

Stop the container with:

```
CTRL + C
```

---

# 6. What is the problem?

The final image contains:

```
Node.js
npm
source files
build tools
serve
built website
```

But our application only needs the final HTML file.

Question:

Why should build tools not necessarily be included in the final production image?

Answer:

```
____________________________________________________

____________________________________________________
```

---

# 7. Multi-Stage Build

We will now use **two stages**.

```
Stage 1
BUILD

Node.js
   ↓
npm run build
   ↓
dist/index.html


Stage 2
RUNTIME

Nginx
   ↓
index.html
```

Create a file called:

```
Dockerfile
```

Start with:

```
# Stage 1: Build

FROM node:22 AS builder

WORKDIR /app

COPY . .

RUN npm run build
```

Now add a second stage.

Use:

```
FROM nginx:alpine
```

Copy the generated website from the first stage:

```
COPY --from=builder /app/dist /usr/share/nginx/html
```

Your Dockerfile should now contain **two** `**FROM**` **instructions**.

---

# 8. Build the Multi-Stage Image

Build it:

```
docker build \
  -t webapp:multi .
```

Check the images:

```
docker images
```

Complete the table:

|Image|Size|
|---|---|
|`webapp:single`|______ MB|
|`webapp:multi`|______ MB|

Which image is smaller?

```
_________________________________
```

---

# 9. Run the optimized image

Run:

```
docker run \
  --rm \
  -p 8080:80 \
  webapp:multi
```

Open:

```
http://localhost:8080
```

The application should still work.

---

# 10. Understand the important line

Look at:

```
COPY --from=builder /app/dist /usr/share/nginx/html
```

What does:

```
--from=builder
```

mean?

```
____________________________________________________

____________________________________________________
```

---

# 11. Final Comparison

## Single-Stage

```
Source Code
    ↓
Node.js
    ↓
Build
    ↓
Node.js + Build Tools + Application
```

## Multi-Stage

```
Source Code
    ↓
Node.js Builder
    ↓
Build
    ↓
dist/
    ↓
Nginx Runtime
    ↓
Final Application
```

---

# Questions

### 1.

What is a Docker build stage?

```
____________________________________________________
```

### 2.

How do you create a new stage?

```
____________________________________________________
```

### 3.

What does this command do?

```
COPY --from=builder
```

```
____________________________________________________
```

### 4.

Give two advantages of multi-stage builds.

```
1. _________________________________________________

2. _________________________________________________
```

---

# Key Concept

A multi-stage build separates:

```
BUILD environment
```

from:

```
RUNTIME environment
```

The final Docker image only contains what is required to **run the application**.