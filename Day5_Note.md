## DevOps Jenkins & GitHub Actions & N8N - Day 5

### 📋 สารบัญ

1. [Workshop Express Docker Application (ต่อ)](#workshop-express-docker-application)
2. [Setup N8N for Notification](#setup-n8n-for-notification)

## Workshop Express Docker CI/CD Jenkins & GitHub Actions & N8N

<img src="https://miro.medium.com/v2/1*Jr3NFSKTfQWRUyjblBSKeg.png" width="200">

โปรเจ็กต์ Express.js + TypeScript REST API ที่ทำงานใน Docker Container พร้อมด้วย CI/CD Pipeline ผ่าน Jenkins, Github Actions และการแจ้งเตือนผ่าน N8N

- [Express Docker Application](#express-docker-application)
  - [📋 Table of Contents](#-table-of-contents)
  - [🚀 Features](#-features)
  - [🏗️ Project Structure](#️-project-structure)
  - [🛠️ Prerequisites](#️-prerequisites)
  - [⚡ Quick Start](#-quick-start)
    - [1. Clone Repository](#1-clone-repository)
    - [2. Local Development](#2-local-development)
    - [3. Docker Development](#3-docker-development)
  - [🐳 Docker Commands](#-docker-commands)
  - [🧪 Testing](#-testing)
  - [🔄 CI/CD Pipeline](#-cicd-pipeline)
  - [⚡ GitHub Actions](#-github-actions)
  - [📡 API Endpoints](#-api-endpoints)
  - [🔧 Configuration](#-configuration)
  - [📝 Environment Variables](#-environment-variables)
  - [📊 Test Coverage](#-test-coverage)
  - [🤝 Contributing](#-contributing)
  - [📄 License](#-license)

## 🚀 Features

- ✅ **Express.js REST API** - Fast, minimal Node.js web framework
- 🔷 **TypeScript Support** - Type-safe JavaScript development
- 🐳 **Docker Containerization** - Lightweight Alpine-based container
- 🧪 **Jest Testing** - Comprehensive test suite with Supertest
- 🔄 **CI/CD Pipeline** - Automated Jenkins pipeline with deployment
- ⚡ **GitHub Actions** - Modern CI/CD with GitHub-native automation
- 📊 **Test Coverage** - Code coverage reports and analysis
- 📦 **Docker Hub Integration** - Automated image publishing
- 🔔 **Server Deployment** - Automated deployment to remote servers
- 🏷️ **Semantic Versioning** - Build number based tagging
- ⚡ **Hot Reload** - Development with ts-node

## 🏗️ Project Structure

```
express-docker-app/
├── 📁 src/
│   └── 📄 app.ts                   # Main Express application (TypeScript)
├── 📁 tests/
│   └── 📄 app.test.ts              # Jest test suite with Supertest
├── 📁 dist/                       # Compiled JavaScript output
│   └── 📄 app.js                  # Compiled application
├── 📁 node_modules/               # Node.js dependencies
├── 📁 .github/
│   └── 📁 workflows/
│       └── 📄 main.yml               # GitHub Actions workflow
├── 🐳 Dockerfile                  # Docker build configuration
├── 🔧 Jenkinsfile                 # Jenkins CI/CD pipeline
├── ⚙️ jest.config.js              # Jest testing configuration
├── 📄 package.json                # Node.js project configuration
├── 📄 package-lock.json           # Dependency lock file
├── 📄 tsconfig.json               # TypeScript configuration
└── 📖 README.md                   # Project documentation
```
## 🛠️ Prerequisites

- **Node.js 22+** (LTS recommended)
- **npm 10+** or **yarn**
- **TypeScript** (installed globally or via npx)
- **Docker & Docker Compose**
- **Git**
- **Jenkins** (for CI/CD)
- **SSH Access** (for deployment)

### เตรียม Jenkins Server บน Docker

1. แก้ไขโปรเจ็กต์ basic-jenkins ตามนี้
แก้ไขไฟล์ `Dockerfile` และ docker-compose.yml ดังนี้

2. สร้าง Dockerfile สำหรับ Custom Jenkins image

```Dockerfile
# เริ่มต้นจาก Image Jenkins ที่เราต้องการ
FROM jenkins/jenkins:jdk21

USER root

# Install Docker CLI and Node.js
RUN apt-get update && \
    apt-get install -y apt-transport-https ca-certificates curl gnupg lsb-release && \
    curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null && \
    apt-get update && \
    apt-get install -y docker-ce-cli && \
    curl -fsSL https://deb.nodesource.com/setup_22.x | bash - && \
    apt-get install -y nodejs && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# สร้าง entrypoint script เพื่อแก้สิทธิ์ docker socket ตอน runtime
RUN echo '#!/bin/bash\n\
DOCKER_SOCK="/var/run/docker.sock"\n\
if [ -S "$DOCKER_SOCK" ]; then\n\
    DOCKER_GID=$(stat -c "%g" $DOCKER_SOCK)\n\
    if ! getent group $DOCKER_GID > /dev/null 2>&1; then\n\
        groupadd -g $DOCKER_GID docker\n\
    fi\n\
    usermod -aG $DOCKER_GID jenkins\n\
fi\n\
exec /usr/bin/tini -- /usr/local/bin/jenkins.sh "$@"' > /usr/local/bin/entrypoint.sh && \
    chmod +x /usr/local/bin/entrypoint.sh

USER jenkins

ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]
```

3. แก้ไข docker-compose.yml

```yaml
# Define Network
networks:
  jenkins_network:
    name: jenkins_network
    driver: bridge

# Define Services
services:
  jenkins:
    build: .
    image: jenkins-with-docker:jdk21
    container_name: jenkins
    user: root # Run as root to allow entrypoint script to set permissions
    volumes:
      - ./jenkins_home:/var/jenkins_home # เพื่อให้ Jenkins สามารถเก็บข้อมูลไว้ใน host
      - /var/run/docker.sock:/var/run/docker.sock # เพื่อให้ Jenkins สามารถใช้งาน Docker daemon ที่รันบน host ได้
    environment:
      - JENKINS_OPTS=--httpPort=8800 # กำหนด Port สำหรับ Jenkins UI
    ports:
      - "8800:8800" # สำหรับ Jenkins UI
    restart: always
    networks:
      - jenkins_network
```

4. run docker-compose

ลบ container เก่า (ถ้ามี)
```bash
docker-compose down --volumes
```

สร้างและรัน Jenkins server
```bash
docker-compose up -d --build
```

## ⚡ Quick Start

#### 1. เตรียมโปรเจ็กต์ nodejs express docker application

1.1 สร้างโฟลเดอร์โปรเจ็กต์ใหม่
```bash
mkdir express-docker-app
cd express-docker-app
```
1.2 สร้างไฟล์ `package.json`
```bash
npm init -y
```
1.3 กำหนดค่าใน `package.json`
```json
{
  "name": "express-docker-app",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "start:ts": "ts-node src/app.ts",
    "start": "node dist/app.js",
    "build": "tsc",
    "test": "jest"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "type": "commonjs",
  "dependencies": {
    "express": "^5.1.0"
  },
  "devDependencies": {
    "@types/express": "^5.0.3",
    "@types/jest": "^30.0.0",
    "@types/node": "^24.5.2",
    "@types/supertest": "^6.0.3",
    "jest": "^30.1.3",
    "supertest": "^7.1.4",
    "ts-jest": "^29.4.4",
    "ts-node": "^10.9.2",
    "typescript": "^5.9.2"
  }
}
```

1.4 ติดตั้ง dependencies
```bash
npm install
```

1.5 กำหนดค่า TypeScript
```bash
npx tsc --init
```

1.6 แก้ไข `tsconfig.json`
```json
{
  "compilerOptions": {
    "target": "es2016",
    "module": "commonjs",
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "skipLibCheck": true,
    "outDir": "dist",
    "rootDir": "src",
    "types": ["jest", "node"]
  },
  // คอมไพล์เฉพาะโค้ดแอป; การทดสอบจะถูกแปลงด้วย ts-jest แยกต่างหาก
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

1.7 สร้างโฟลเดอร์ `src` และไฟล์ `app.ts`
```bash
mkdir src
touch src/app.ts
```

1.8 เพิ่มโค้ดตัวอย่างใน `src/app.ts`
```typescript
import express, { type Express, type Request, type Response } from 'express'

const app: Express = express()

const port: number = 3000

// Routes
// GET /
app.get('/', (_: Request, res: Response) => {
  res.json({
    message: 'Hello Express + TypeScript!'
  })
})

// GET /api/hello
app.get('/api/hello', (_: Request, res: Response) => {
  res.json({
    message: 'Hello from Express API!'
  })
})

// GET /api/health
app.get('/api/health', (_: Request, res: Response) => {
  res.json({
    status: 'UP'
  })
})

// Start server
app.listen(port, () => console.log(`Application is running on port ${port}`))
```

1.9 สร้าง tests folder และไฟล์ `app.test.ts`
```bash
mkdir tests
touch tests/app.test.ts
```

1.10 เพิ่มโค้ดตัวอย่างใน `tests/app.test.ts`
```typescript
import request from 'supertest'
import express from 'express'

const app = express()
app.get('/api/hello', (_, res) => res.json({ message: 'Hello from Express API!' }))

test('GET /api/hello', async () => {
  const res = await request(app).get('/api/hello')
  expect(res.statusCode).toBe(200)
  expect(res.body.message).toBe('Hello from Express API!')
})
```
1.11 กำหนดค่า Jest โดยสร้างไฟล์ `jest.config.js`
```javascript
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'node',
  roots: ['<rootDir>/src', '<rootDir>/tests'],
  testMatch: ['**/__tests__/**/*.ts', '**/?(*.)+(spec|test).ts'],
  transform: {
    '^.+\\.ts$': 'ts-jest',
  },
  collectCoverageFrom: [
    'src/**/*.ts',
    '!src/**/*.d.ts',
  ],
}
```
1.12 สร้างไฟล์ `.gitignore`
```bash
touch .gitignore
```
1.13 เพิ่มโค้ดใน `.gitignore`
```
node_modules
dist
.env
```


#### 2. เตรียม Dockerfile สำหรับโปรเจ็กต์

2.1 สร้างไฟล์ `Dockerfile`
```bash
touch Dockerfile
```

2.2 เพิ่มโค้ดใน `Dockerfile`
```Dockerfile
# Build stage - สำหรับ development และ testing
FROM node:22-alpine AS builder

# กำหนด Working Directory ภายใน Container
WORKDIR /app

# Copy ไฟล์ package.json และ package-lock.json เข้าไปก่อน
# เพื่อใช้ประโยชน์จาก Docker cache layer ทำให้ไม่ต้อง install dependencies ใหม่ทุกครั้งที่แก้โค้ด
COPY package*.json ./

# ติดตั้ง Dependencies (รวม dev dependencies สำหรับ testing)
RUN npm install

# Copy โค้ดทั้งหมดในโปรเจกต์เข้าไปใน container
COPY . .

# Compile TypeScript เป็น JavaScript
RUN npm run build

# Production stage - สำหรับ production deployment
FROM node:22-alpine AS production

# กำหนด Working Directory ภายใน Container
WORKDIR /app

# Copy package files
COPY package*.json ./

# ติดตั้งเฉพาะ production dependencies
RUN npm ci --only=production && npm cache clean --force

# Copy โค้ดที่ compiled แล้วจาก builder stage
COPY --from=builder /app/dist ./dist
# COPY --from=builder /app/src ./src

# กำหนด Port ที่ Container จะทำงาน
EXPOSE 3000

# คำสั่งสำหรับรัน Express Application (ใช้ compiled JavaScript)
CMD ["npm", "start"]
```

2.3 สร้างไฟล์ `.dockerignore`
```bash
# Dependencies
node_modules
npm-debug.log*

# Build outputs
dist
build

# Environment files
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# Testing
coverage
*.lcov

# Git
.git
.gitignore

# Docker
Dockerfile
.dockerignore

# Documentation
README.md
*.md

# IDE
.vscode
.idea
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Logs
logs
*.log

# Temporary files
.tmp
.temp
```

#### 3. สร้าง Jenkinsfile สำหรับ CI/CD Pipeline
```bash
touch Jenkinsfile
```

3.1 เพิ่มโค้ดใน `Jenkinsfile`
```groovy
pipeline {
    // ใช้ agent any เพราะ build จะทำงานบน Jenkins controller (Linux container) อยู่แล้ว
    agent any

    // กัน “เช็คเอาต์ซ้ำซ้อน”
    // ถ้า job เป็นแบบ Pipeline from SCM / Multibranch แนะนำเพิ่ม options { skipDefaultCheckout(true) }
    // เพื่อปิดการ checkout อัตโนมัติก่อนเข้า stages (เพราะเรามี checkout scm อยู่แล้ว)
    options { 
        skipDefaultCheckout(true)   // ถ้าเป็น Pipeline from SCM/Multi-branch
    }

    // กำหนด environment variables
    environment {
        DOCKER_HUB_CREDENTIALS_ID = 'dockerhub-cred'
        DOCKER_REPO               = "iamsamitdev/express-docker-app-jenkins"
        APP_NAME                  = "express-docker-app-jenkins"
    }

    // กำหนด stages ของ Pipeline
    stages {

        // Stage 1: ดึงโค้ดล่าสุดจาก Git
        // ใช้ checkout scm หากใช้ Pipeline from SCM
        // หรือใช้ git url: 'https://github.com/your-username/your-repo.git'
        stage('Checkout') {
            steps {
                echo "Checking out code..."
                checkout scm
                // หรือใช้แบบกำหนดเอง หากไม่ใช้ Pipeline from SCM:
                // git url: 'https://github.com/your-username/your-repo.git'
            }
        }

        // Stage 2: ติดตั้ง dependencies และ Run test
        // ใช้ Node.js plugin (ต้องติดตั้ง NodeJS plugin ก่อน) ใน Jenkins หรือ Node.js ใน Docker 
        // ถ้ามี package-lock.json ให้ใช้ npm ci แทน npm install จะเร็วและล็อกเวอร์ชันชัดเจนกว่า
        stage('Install & Test') {
            steps {
                sh '''
                    if [ -f package-lock.json ]; then npm ci; else npm install; fi
                    npm test
                '''
            }
        }

        // Stage 3: สร้าง Docker Image
        // ใช้ Docker ที่ติดตั้งบน Jenkins agent (ต้องติดตั้ง Docker plugin ก่อน) ใน Jenkins หรือ Docker ใน Docker
        stage('Build Docker Image') {
            steps {
                sh """
                    echo "Building Docker image: ${DOCKER_REPO}:${BUILD_NUMBER}"
                    docker build --target production -t ${DOCKER_REPO}:${BUILD_NUMBER} -t ${DOCKER_REPO}:latest .
                """
            }
        }

        // Stage 4: Push Image ไปยัง Docker Hub
        // ใช้ Docker Hub credentials ที่ตั้งค่าไว้ใน Jenkins
        // DOCKER_USER และ DOCKER_PASS กำหนดไว้ที่ไหน?
        // ใน Jenkins Credentials (Username with password) โดยใช้ ID ที่กำหนดใน DOCKER_HUB_CREDENTIALS_ID ข้างบน
        // ตัวแปร DOCKER_USER และ DOCKER_PASS ไม่ได้ถูกกำหนดไว้ที่ไหนล่วงหน้าครับ แต่มันถูก "สร้างขึ้นมาชั่วคราว" โดยฟังก์ชัน withCredentials เอง
        // เมื่อเจอแล้ว มันจะนำค่า username และ password จาก Credential นั้นออกมา
        // ปลอดภัยหรือไม่? Password จะแสดงใน Log หรือเปล่า?
        // ปลอดภัยมาก และ Password จะไม่แสดงใน Log 
        // Jenkins ฉลาดพอที่จะรู้ว่าค่าที่มาจาก withCredentials เป็นข้อมูลลับ (Secret) ต่อให้คุณใช้คำสั่ง echo "${DOCKER_PASS}" Jenkins ก็จะ ไม่แสดงค่า Password จริงๆ ใน Console Log แต่จะแสดงเป็น ******** แทนโดยอัตโนมัติ
        // การทำงานของ Pipe | และ --password-stdin
        // echo "\${DOCKER_PASS}": คำสั่งนี้จะส่งค่า Password จริงๆ ออกไป
        // | (Pipe): แต่แทนที่จะส่งไปที่หน้าจอ Log, เครื่องหมาย pipe จะ ส่งต่อ (redirect) ผลลัพธ์ของ echo ไปเป็น Input (stdin) ของคำสั่งถัดไปทันที
        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: env.DOCKER_HUB_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo "Logging into Docker Hub..."
                        echo "\${DOCKER_PASS}" | docker login -u "\${DOCKER_USER}" --password-stdin
                        echo "Pushing image to Docker Hub..."
                        docker push ${DOCKER_REPO}:${BUILD_NUMBER}
                        docker push ${DOCKER_REPO}:latest
                        docker logout
                    """
                }
            }
        }

        // Stage 5: เคลียร์ Docker images บน agent
        // เพื่อประหยัดพื้นที่บน Jenkins agent หลังจาก push image ขึ้น Docker Hub แล้ว
        // ไม่จำเป็นต้องเก็บ image ไว้บน agent อีกต่อไป
        // หลักการทำงานคือ ลบ image ที่สร้างขึ้น (ทั้งแบบมี tag build number และ latest)
        // และลบ cache ที่ไม่จำเป็นออกไป
        stage('Cleanup Docker') {
            steps {
                sh """
                    echo "Cleaning up local Docker images/cache on agent..."
                    docker image rm -f ${DOCKER_REPO}:${BUILD_NUMBER} || true
                    docker image rm -f ${DOCKER_REPO}:latest || true
                    docker image prune -af || true
                    docker builder prune -af || true
                """
            }
        }

        // Stage 6: Deploy ไปยังเครื่อง local
        // ดึง image ล่าสุดจาก Docker Hub มาใช้งาน
        // หยุดและลบ container เก่าที่ชื่อ ${APP_NAME} (ถ้ามี)
        // สร้างและรัน container ใหม่จาก image ล่าสุด
        stage('Deploy Local') {
            steps {
                sh """
                    echo "Deploying container ${APP_NAME} from latest image..."
                    docker pull ${DOCKER_REPO}:latest
                    docker stop ${APP_NAME} || true
                    docker rm ${APP_NAME} || true
                    docker run -d --name ${APP_NAME} -p 3000:3000 ${DOCKER_REPO}:latest
                    docker ps --filter name=${APP_NAME} --format "table {{.Names}}\\t{{.Image}}\\t{{.Status}}"
                """
            }
        }
    }

    // กำหนด post actions
    // เช่น การแจ้งเตือนเมื่อ pipeline เสร็จสิ้น
    // สามารถเพิ่มการแจ้งเตือนผ่าน email, Slack, หรืออื่นๆ ได้ตามต้องการ
    post {
        always {
            echo "Pipeline finished with status: ${currentBuild.currentResult}"
        }
        success {
            echo "Pipeline succeeded!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }

}
```

#### 4. Push โค้ดขึ้น GitHub

```bash
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/your-username/your-repo.git
git push -u origin main
```

#### 5. สร้าง Jenkins Job แบบ Pipeline
5.1 ไปที่ Jenkins Dashboard
5.2 คลิกที่ "New Item"
5.3 ตั้งชื่อ Job และเลือก "Pipeline" จากนั้นคลิก "OK"
5.4 ในส่วน "Pipeline" ให้เลือก "Pipeline script from SCM"
5.5 ตั้งค่า SCM เป็น "Git" และกรอก URL ของ GitHub Repo
5.6 ตั้งค่า "Branch Specifier" เป็น "*/main"
5.7 ในส่วน "Script Path" ให้ระบุที่อยู่ของ Jenkinsfile (เช่น `Jenkinsfile`)
5.8 คลิก "Save"
5.9 คลิก "Build Now" เพื่อทดสอบการทำงานของ Pipeline

#### 5. ติดตั้ง localtunnel (ถ้ายังไม่มี)
```bash
npm install -g localtunnel
```

#### 6. รัน localtunnel เพื่อเปิดเผย Jenkins Server
```bash
lt --port 8800
```

#### 7. การตั้งค่า Webhook ใน GitHub
6.1 ไปที่หน้า GitHub Repository ของคุณ
6.2 คลิกที่ "Settings" > "Webhooks" > "Add webhook"
6.3 ในช่อง "Payload URL" ให้ใส่ URL ของ Jenkins Server ตามด้วย `/github-webhook/` (เช่น `http://your-jenkins-url/github-webhook/`)
6.4 เลือก "Content type" เป็น `application/json`
6.5 เลือก "Just the push event."
6.6 คลิก "Add webhook"

#### 8. ทดสอบการทำงาน
8.1 ทำการแก้ไขโค้ดในโปรเจ็กต์ของคุณ (เช่น แก้ไขไฟล์ `src/app.ts`)
8.2 Commit และ Push การเปลี่ยนแปลงขึ้น GitHub
```bash
git add .
git commit -m "Test Jenkins CI/CD"
git push origin main
```
---

## Setup N8N for Notification

1. สมัครบัญชี ngrok ฟรีที่ https://ngrok.com/ และคัดลอก Authtoken ของคุณมาเก็บไว้

2. สร้างโฟลเดอร์ใหม่สำหรับ n8n
```bash
mkdir n8n-postgres-ngrok
```

3. สร้างไฟล์ `.env` ในโฟลเดอร์ `n8n-postgres-ngrok`
```bash
cd n8n-postgres-ngrok
touch .env
```
4. เพิ่มโค้ดในไฟล์ `.env`

```env
# PostgreSQL Credentials
POSTGRES_DB=n8n
POSTGRES_USER=admin
POSTGRES_PASSWORD=your_password_here

# n8n Encryption Key (สำคัญมาก ห้ามทำหาย)
# สร้างคีย์สุ่มยาวๆ ได้จาก: openssl rand -hex 32
N8N_ENCRYPTION_KEY=0123456789abcdef0123456789abcdef

# Timezone Settings
GENERIC_TIMEZONE=Asia/Bangkok
TZ=Asia/Bangkok

# ngrok Settings
#  สมัคร ngrok ฟรีได้ที่: https://dashboard.ngrok.com/signup
NGROK_AUTHTOKEN=your_ngrok_authtoken_here
```

5. สร้างไฟล์ `docker-compose.yml` ในโฟลเดอร์ `n8n-postgres-ngrok`
```bash
touch docker-compose.yml
```

6. เพิ่มโค้ดในไฟล์ `docker-compose.yml`
```yaml
networks:
  n8n_network:
    name: n8n_network
    driver: bridge

services:

  # service สำหรับ PostgreSQL
  postgres:
    image: postgres:16
    container_name: n8n_postgres
    restart: always
    environment:
      - POSTGRES_USER=${POSTGRES_USER}
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
      - POSTGRES_DB=${POSTGRES_DB}
    volumes:
      - ./postgres_data:/var/lib/postgresql/data
    networks:
      - n8n_network

  # service สำหรับ n8n
  n8n:
    image: docker.n8n.io/n8nio/n8n
    container_name: n8n_main
    restart: always
    ports:
      - "5678:5678"
    environment:
      - DB_TYPE=postgresdb
      - DB_POSTGRESDB_HOST=postgres
      - DB_POSTGRESDB_PORT=5432
      - DB_POSTGRESDB_DATABASE=${POSTGRES_DB}
      - DB_POSTGRESDB_USER=${POSTGRES_USER}
      - DB_POSTGRESDB_PASSWORD=${POSTGRES_PASSWORD}
      - N8N_ENCRYPTION_KEY=${N8N_ENCRYPTION_KEY} # ต้องตั้งค่านี้เพื่อความปลอดภัย
      - GENERIC_TIMEZONE=${GENERIC_TIMEZONE}
      - TZ=${TZ}
      - N8N_HOST=localhost # จำเป็นเพื่อให้ Tunnel ทำงานถูกต้อง
    volumes:
      - ./n8n_data:/home/node/.n8n
    networks:
      - n8n_network
    depends_on:
      - postgres

  # service สำหรับ ngrok
  ngrok:
    image: ngrok/ngrok:latest
    container_name: n8n_ngrok_tunnel
    restart: unless-stopped
    environment:
      - NGROK_AUTHTOKEN=${NGROK_AUTHTOKEN}
    command: http n8n:5678
    ports:
      - "4040:4040" # สำหรับเข้าดูหน้า Web UI
    networks:
      - n8n_network
    depends_on:
      - n8n
```

7. รัน n8n, PostgreSQL และ ngrok ด้วย Docker Compose
```bash
docker-compose up -d
```

8. ตรวจสอบสถานะ container ทั้งหมด
```bash
docker-compose ps
```

9. เข้าใช้งาน n8n ผ่าน URL ที่ ngrok สร้างให้
- เปิดเบราว์เซอร์แล้วไปที่ http://127.0.0.1:4040
- ดูที่ช่อง "Forwarding" จะเห็น URL ที่ ngrok สร้างให้ (เช่น https://abcd1234.ngrok.io)
- คลิกที่ URL นั้นเพื่อเข้าใช้งาน n8n (เช่น https://abcd1234.ngrok.io)
10. ตั้งค่า Webhook ใน n8n
- สร้าง Workflow ใหม่ใน n8n
- เพิ่ม Node "Webhook" และตั้งค่า Method เป็น "POST"
- คัดลอก URL ของ Webhook (เช่น https://abcd1234.ngrok.io/webhook/your-webhook-id)
11. ตั้งค่า Jenkins ให้ส่ง Notification ไปยัง n8n
- ใน Jenkinsfile ของคุณ เพิ่ม stage ใหม่หลังจาก stage 'Deploy Local' ดังนี้

```groovy
stage('Deploy Local') {
    steps {
        ...
    }
    // ส่งข้อมูลไปยัง n8n webhook เมื่อ deploy สำเร็จ
    // ใช้ Jenkins HTTP Request Plugin (ต้องติดตั้งก่อน)
    // หรือใช้ Java URLConnection แทน (fallback) ถ้า httpRequest ไม่ได้ติดตั้ง
    // n8n-webhook คือ Jenkins Secret Text Credential ที่เก็บ URL ของ n8n webhook
    // ต้องสร้าง Credential นี้ใน Jenkins ก่อน ใช้งาน
    // โดยใช้ ID ว่า n8n-webhook

    post {
        success {
            script {
                withCredentials([string(credentialsId: 'n8n-webhook', variable: 'N8N_WEBHOOK_URL')]) {
                    def payload = [
                        project  : env.JOB_NAME,
                        stage    : 'Deploy Local',
                        status   : 'success',
                        build    : env.BUILD_NUMBER,
                        image    : "${env.DOCKER_REPO}:latest",
                        container: env.APP_NAME,
                        url      : 'http://localhost:3000/',
                        timestamp: new Date().format("yyyy-MM-dd'T'HH:mm:ssXXX")
                    ]
                    def body = groovy.json.JsonOutput.toJson(payload)
                    try {
                        httpRequest acceptType: 'APPLICATION_JSON',
                                    contentType: 'APPLICATION_JSON',
                                    httpMode: 'POST',
                                    requestBody: body,
                                    url: N8N_WEBHOOK_URL,
                                    validResponseCodes: '100:599'
                        echo 'n8n webhook (success) sent via httpRequest.'
                    } catch (err) {
                        echo "httpRequest failed or not available: ${err}. Falling back to Java URLConnection..."
                        try {
                            def conn = new java.net.URL(N8N_WEBHOOK_URL).openConnection()
                            conn.setRequestMethod('POST')
                            conn.setDoOutput(true)
                            conn.setRequestProperty('Content-Type', 'application/json')
                            conn.getOutputStream().withWriter('UTF-8') { it << body }
                            int rc = conn.getResponseCode()
                            echo "n8n webhook (success) via URLConnection, response code: ${rc}"
                        } catch (e2) {
                            echo "Failed to notify n8n (success): ${e2}"
                        }
                    }
                }
            }
        }
    }
}

 post {
    always {
        echo "Pipeline finished with status: ${currentBuild.currentResult}"
    }
    success {
        echo "Pipeline succeeded!"
    }
    failure {
        // ส่งข้อมูลไปยัง n8n webhook เมื่อ pipeline ล้มเหลว
        // ใช้ Jenkins HTTP Request Plugin (ต้องติดตั้งก่อน)
        // หรือใช้ Java URLConnection แทน (fallback) ถ้า httpRequest ไม่ได้ติดตั้ง
        // n8n-webhook คือ Jenkins Secret Text Credential ที่เก็บ URL ของ n8
        // ต้องสร้าง Credential นี้ใน Jenkins ก่อน ใช้งาน
        // โดยใช้ ID ว่า n8n-webhook
        script {
            withCredentials([string(credentialsId: 'n8n-webhook', variable: 'N8N_WEBHOOK_URL')]) {
                def payload = [
                    project  : env.JOB_NAME,
                    stage    : 'Pipeline',
                    status   : 'failed',
                    build    : env.BUILD_NUMBER,
                    image    : "${env.DOCKER_REPO}:latest",
                    container: env.APP_NAME,
                    url      : 'http://localhost:3000/',
                    timestamp: new Date().format("yyyy-MM-dd'T'HH:mm:ssXXX")
                ]
                def body = groovy.json.JsonOutput.toJson(payload)
                try {
                    httpRequest acceptType: 'APPLICATION_JSON',
                                contentType: 'APPLICATION_JSON',
                                httpMode: 'POST',
                                requestBody: body,
                                url: N8N_WEBHOOK_URL,
                                validResponseCodes: '100:599'
                    echo 'n8n webhook (failure) sent via httpRequest.'
                } catch (err) {
                    echo "httpRequest failed or not available: ${err}. Falling back to Java URLConnection..."
                    try {
                        def conn = new java.net.URL(N8N_WEBHOOK_URL).openConnection()
                        conn.setRequestMethod('POST')
                        conn.setDoOutput(true)
                        conn.setRequestProperty('Content-Type', 'application/json')
                        conn.getOutputStream().withWriter('UTF-8') { it << body }
                        int rc = conn.getResponseCode()
                        echo "n8n webhook (failure) via URLConnection, response code: ${rc}"
                    } catch (e2) {
                        echo "Failed to notify n8n (failure): ${e2}"
                    }
                }
            }
        }
    }
}
```

11. กำหนดตัวแปร Jenkins Credential
- ไปที่ Jenkins Dashboard > Manage Jenkins > Manage Credentials
- คลิกที่ (global) > Add Credentials
- เลือก Kind เป็น Secret text
- กรอกข้อมูลในช่อง Secret เป็น URL ของ n8n webhook
- กำหนด ID ว่า n8n-webhook
- คลิก OK เพื่อบันทึก

12. ติดตั้ง Jenkins HTTP Request Plugin
- ไปที่ Jenkins Dashboard > Manage Jenkins > Manage Plugins
- ในแท็บ Available ค้นหา "HTTP Request"
- เลือกและติดตั้ง จากนั้นรีสตาร์ท Jenkins

13. สร้าง node code ใน n8n
- ใน n8n Workflow ที่สร้างไว้ เพิ่ม Node "Set" เพื่อกำหนดค่าข้อมูลที่ต้องการส่งไปยัง Slack
- เพิ่ม Node "Slack" เพื่อส่งข้อความแจ้งเตือน

```javascript
// Normalize input from Webhook node
// n8n Webhook node โดยปกติจะให้ { body, headers, query, params }
// แต่เผื่อกรณี payload ถูก map ขึ้นมาบน root ก็รองรับทั้งสองแบบ

const items = $input.all();
if (items.length === 0) {
  return [{ json: { error: 'No input items from Webhook' } }];
}

const raw = items[0].json || {};
const payload = (raw.body && typeof raw.body === 'object') ? raw.body : raw;

// Extract fields with sane defaults
const project   = String(payload.project ?? payload.job ?? 'unknown-project');
const stage     = String(payload.stage   ?? 'unknown-stage');
const status    = String(payload.status  ?? 'unknown');
const build     = String(payload.build   ?? payload.buildNumber ?? 'n/a');
const image     = String(payload.image   ?? 'n/a');
const container = String(payload.container ?? 'n/a');
const url       = String(payload.url     ?? 'http://localhost:3000/');
const timestamp = payload.timestamp ? new Date(payload.timestamp).toISOString() : new Date().toISOString();

// Small helpers
const emoji = status.toLowerCase() === 'success' ? '✅'
            : status.toLowerCase() === 'failed'  ? '❌'
            : 'ℹ️';

const lines = [
  `${emoji} Deploy ${status.toUpperCase()}: ${project} (${stage})`,
  `Build: ${build}`,
  `Image: ${image}`,
  `Container: ${container}`,
  `URL: ${url}`,
  `Time: ${timestamp}`
];
const slackText = lines.join('\n');

// Optional: Slack Block Kit (ถ้าคุณจะ map ไปใช้กับ Slack node แบบ Blocks)
const slackBlocks = [
  {
    type: 'header',
    text: { type: 'plain_text', text: `${emoji} ${project} – ${stage}` }
  },
  { type: 'divider' },
  {
    type: 'section',
    fields: [
      { type: 'mrkdwn', text: `*Status:*\n${status.toUpperCase()}` },
      { type: 'mrkdwn', text: `*Build:*\n${build}` },
      { type: 'mrkdwn', text: `*Image:*\n${image}` },
      { type: 'mrkdwn', text: `*Container:*\n${container}` },
      { type: 'mrkdwn', text: `*URL:*\n${url}` },
      { type: 'mrkdwn', text: `*Time:*\n${timestamp}` }
    ]
  }
];

// Return a single normalized item
return [{
  json: {
    // raw webhook data (for debugging)
    _webhook: raw,

    // normalized
    project, stage, status, build, image, container, url, timestamp,

    // for Slack node
    slack: {
      text: slackText,
      blocks: slackBlocks
    }
  }
}];
```

14. ตั้งค่า Slack ให้กับ node “Send a message” ใน n8n 
- ไปที่ https://api.slack.com/apps และสร้างแอปใหม่
- ตั้งค่า OAuth & Permissions โดยเพิ่ม Scopes ที่ต้องการ เช่น chat:write, incoming-webhook
- ติดตั้งแอปใน workspace ของคุณและคัดลอก OAuth Access Token
- ใน n8n เพิ่ม Node "Slack" และตั้งค่า Credentials โดยใช้ OAuth Access Token ที่ได้มา
- เชื่อมต่อ Node "Webhook" กับ Node "Slack" เพื่อส่งข้อความแจ้งเตือนเมื่อมีการเรียก Webhook

15. ตั้งค่า ส่งผ่าน n8n ไป Discord
- ใน Discord สร้าง Webhook URL สำหรับช่องที่ต้องการส่งข้อความ
- ใน n8n เพิ่ม Node "HTTP Request" หลังจาก Node "Set"
- ตั้งค่า HTTP Request ดังนี้
  - Method: POST
  - URL: [Webhook URL ที่สร้างใน Discord]
  - Body: JSON
  - JSON Body:
    ```json
    {
      "content": "ข้อความที่ต้องการส่งไปยัง Discord"
    }
    ```
- เชื่อมต่อ Node "Set" กับ Node "HTTP Request" เพื่อส่งข้อความไปยัง Discord เมื่อมีการเรียก Webhook

16. ตั้งค่า ส่งผ่าน Line Messaging API
- สร้าง Channel ใน LINE Developers Console และคัดลอก Channel Access Token
- ใน n8n เพิ่ม Node "HTTP Request" หลังจาก Node "Set"
- ตั้งค่า HTTP Request ดังนี้
  - Method: POST
  - URL: https://api.line.me/v2/bot/message/push
  - Headers:
    - Authorization: Bearer [Channel Access Token]
    - Content-Type: application/json
    - Body: JSON
    - JSON Body:
      ```json
      {
        "to": "[User ID หรือ Group ID]",
        "messages": [
          {
            "type": "text",
            "text": "ข้อความที่ต้องการส่งไปยัง LINE"
          }
        ]
      }
      ```
- เชื่อมต่อ Node "Set" กับ Node "HTTP Request" เพื่อส่งข้อความไปยัง LINE เมื่อมีการเรียก Webhook

17. ตั้งค่าแจ้งเตือนผ่าน Email
- ใน n8n เพิ่ม Node "Email" หลังจาก Node "Set"
- ตั้งค่า Email ดังนี้
  - To: [ที่อยู่อีเมลผู้รับ]
  - Subject: [หัวข้ออีเมล]
  - Body: [เนื้อหาอีเมล]
- เชื่อมต่อ Node "Set" กับ Node "Email" เพื่อส่งอีเมลแจ้งเตือนเมื่อมีการเรียก Webhook

---

## สิ่งที่เรียนรู้ใน Day 5

✅ การติดตั้งและตั้งค่า Jenkins เบื้องต้น
✅ การสร้าง Pipeline แบบพื้นฐานใน Jenkins
✅ การใช้งาน Jenkinsfile
✅ การเชื่อมต่อ Jenkins กับ GitHub
✅ การตั้งค่า Webhook ใน GitHub เพื่อ Trigger Jenkins Job
✅ การใช้งาน Jenkins Plugins ที่จำเป็น
✅ การตั้งค่า Jenkins Credentials สำหรับการเข้าถึง GitHub
✅ การสร้าง Multi-Branch Pipeline ใน Jenkins
✅ การตั้งค่า Jenkins เพื่อทำงานร่วมกับ Docker

---

## หมายเหตุ

- ใช้ Node.js เวอร์ชั่น 22.x
- ใช้ Java JDK เวอร์ชั่น 17 หรือ 21 เท่านั้น (เก่าหรือใหม่กว่านี้ไม่รองรับ)
- ใช้ Python เวอร์ชั่น 3.10 ขึ้นไป
- ใช้ Docker Desktop เวอร์ชั่นล่าสุด
- ใช้ Git เวอร์ชั่นล่าสุด
- ใช้ Visual Studio Code เวอร์ชั่นล่าสุด
- ใช้ Git Bash (สำหรับ Windows) หรือ Terminal (สำหรับ Mac/Linux)

## สรุป
ขอให้สนุกกับการเรียนรู้ DevOps Jenkins & GitHub Actions & N8N ครับ!