## DevOps Jenkins & GitHub Actions & N8N - Day 4

### 📋 สารบัญ
1. [พื้นฐาน CI/CD ด้วย Jenkins (ต่อ)](#พื้นฐาน-ci/cd-ด้วย-jenkins)
2. [การสร้าง Pipeline ด้วย Jenkins](#การสร้าง-pipeline-ด้วย-jenkins)
3. [การเชื่อมต่อ Jenkins กับ GitHub](#การเชื่อมต่อ-jenkins-กับ-github)
4. [การตั้งค่า Webhook ใน GitHub เพื่อ Trigger Jenkins Job](#การตั้งค่า-webhook-ใน-github-เพื่อ-trigger-jenkins-job)
5. [การใช้งาน Jenkins Plugins ที่จำเป็น](#การใช้งาน-jenkins-plugins-ที่จำเป็น)
6. [การตั้งค่า Jenkins Credentials สำหรับการเข้าถึง GitHub](#การตั้งค่า-jenkins-credentials-สำหรับการเข้าถึง-github)
7. [การสร้าง Multi-Branch Pipeline ใน Jenkins](#การสร้าง-multi-branch-pipeline-ใน-jenkins)
8. [การตั้งค่า Jenkins เพื่อทำงานร่วมกับ Docker](#การตั้งค่า-jenkins-เพื่อทำงานร่วมกับ-docker)

---

## พื้นฐาน CI/CD ด้วย Jenkins (ต่อ)

- Jenkins เป็นเครื่องมือที่ช่วยในการทำ CI/CD โดยอัตโนมัติ
- CI/CD คือแนวทางการพัฒนาซอฟต์แวร์ที่ช่วยให้การพัฒนาและการนำส่งซอฟต์แวร์เป็นไปอย่างรวดเร็วและมีประสิทธิภาพ
- Jenkins รองรับการทำงานร่วมกับเครื่องมือและเทคโนโลยีต่าง ๆ เช่น Git, Docker, Kubernetes เป็นต้น
- Jenkins มีระบบ Plugin ที่ช่วยเพิ่มความสามารถและฟีเจอร์ต่าง ๆ ให้กับ Jenkins
- Jenkins สามารถทำงานร่วมกับระบบ Version Control เช่น GitHub, GitLab, Bitbucket เป็นต้น
- Jenkins รองรับการสร้าง Pipeline ที่ช่วยในการจัดการกระบวนการ CI/CD ได้อย่างมีประสิทธิภาพ
- Jenkins มีระบบการแจ้งเตือนผ่านทาง Email, Slack, Microsoft Teams เป็นต้น
- Jenkins รองรับการทำงานร่วมกับ Docker เพื่อสร้างสภาพแวดล้อมที่เหมาะสมสำหรับการทดสอบและการนำส่งซอฟต์แวร์
- Jenkins มีระบบการจัดการผู้ใช้งานและสิทธิ์การเข้าถึงที่ช่วยให้การบริหารจัดการ Jenkins เป็นไปอย่างมีประสิทธิภาพ
- Jenkins รองรับการทำงานร่วมกับ Cloud Providers เช่น AWS, Azure, Google Cloud เป็นต้น
- Jenkins มีระบบการสำรองข้อมูลและการกู้คืนข้อมูลที่ช่วยให้การบริหารจัดการ Jenkins เป็นไปอย่างมีประสิทธิภาพ

---

## What is Jenkins Pipeline ?

![Jenkins Pipeline](https://kubedemy.io/wp-content/uploads/2023/06/4418c3cd93a28e984510f8d25a6fd815.png)

Jenkins Pipeline (หรือเรียกสั้นๆ ว่า "Pipeline" โดยใช้ตัวพิมพ์ใหญ่ "P") เป็นชุดของปลั๊กอินที่สนับสนุนการนำเสนอกระบวนการและการผนวกรวม pipeline สำหรับ continuous delivery เข้าไปใน Jenkins

Continuous delivery (CD) pipeline เป็นการแสดงกระบวนการอัตโนมัติที่ช่วยนำซอฟต์แวร์จากระบบควบคุมเวอร์ชันไปจนถึงมือผู้ใช้และลูกค้าของคุณ ทุกครั้งที่มีการเปลี่ยนแปลงซอฟต์แวร์ (ที่ถูก commit ในระบบควบคุมเวอร์ชัน) จะต้องผ่านกระบวนการที่ซับซ้อนเพื่อให้สามารถนำออกใช้งานได้ กระบวนการนี้รวมถึงการสร้างซอฟต์แวร์ในลักษณะที่เชื่อถือได้และสามารถทำซ้ำได้ รวมถึงการนำซอฟต์แวร์ที่สร้างเสร็จแล้ว (เรียกว่า "build") ผ่านหลายขั้นตอนของการทดสอบและการปรับใช้

Pipeline ช่วยให้คุณสามารถกำหนดกระบวนการเหล่านี้ในรูปแบบที่เป็นโค้ด ซึ่งทำให้สามารถจัดการและปรับปรุงกระบวนการได้ง่ายขึ้น นอกจากนี้ยังช่วยให้สามารถทำงานร่วมกับทีมพัฒนาและทีมปฏิบัติการได้อย่างมีประสิทธิภาพมากขึ้น

## ประเภทของ Jenkins Pipeline

![Jenkins Pipeline Types](https://kouzie.github.io/assets/cicd/jenkins1.png)

การเขียน Pipeline ใน Jenkins สามารถทำได้หลัก ๆ 2 แบบ คือ Declarative Pipeline และ Scripted Pipeline โดยแต่ละแบบมีลักษณะและการใช้งานที่แตกต่างกัน ดังนี้

![Jenkins Pipeline Types](https://www.lambdatest.com/blog/wp-content/uploads/2021/04/Screenshot-2021-02-13-at-10.05.27-AM.png)

1. Declarative Pipeline: เป็นรูปแบบที่มีโครงสร้างชัดเจนและง่ายต่อการอ่านและเขียน โดยใช้คำสั่งที่กำหนดไว้ล่วงหน้า เช่น `pipeline`, `stage`, `steps` เป็นต้น

![Jenkins Declarative Pipeline](https://devops.com/wp-content/uploads/2018/07/Jenkinspic4-1.png)

2. Scripted Pipeline: เป็นรูปแบบที่มีความยืดหยุ่นมากกว่า Declarative Pipeline โดยใช้ Groovy เป็นภาษาหลักในการเขียนโค้ด ซึ่งช่วยให้สามารถเขียนโค้ดที่ซับซ้อนและมีความยืดหยุ่นมากขึ้น

## การสร้าง Pipeline ด้วย Jenkins
1. ติดตั้ง Jenkins และ Plugins ที่จำเป็น
2. สร้าง Jenkins Job ใหม่
3. เลือกประเภทของ Job เป็น "Pipeline"
4. กำหนดชื่อ Job และตั้งค่าอื่น ๆ ตามต้องการ

## ตัวอย่างการสร้าง Jenkinsfile แบบ Declarative Pipeline

```groovy
pipeline {
    agent any // ใช้ agent ใดก็ได้
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
                // คำสั่งสำหรับการ build เช่น การ compile โค้ด
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
                // คำสั่งสำหรับการทดสอบ เช่น การรัน unit tests
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
                // คำสั่งสำหรับการ deploy เช่น การส่งโค้ดไปยังเซิร์ฟเวอร์
            }
        }
    }
}

```
อธิบายโค้ด:
- `pipeline { ... }`: กำหนดว่าเป็น Jenkins Pipeline
- `agent any`: กำหนดให้ Jenkins ใช้ agent ใดก็ได้ในการรัน Pipeline
- `stages { ... }`: กำหนดขั้นตอนต่าง ๆ ใน Pipeline
- `stage('Build') { ... }`: กำหนดขั้นตอน "Build"
- `steps { ... }`: กำหนดคำสั่งที่ต้องการรันในแต่ละขั้นตอน

> agent คือเครื่องมือหรือสภาพแวดล้อมที่ Jenkins ใช้ในการรัน Pipeline เช่น Docker, Kubernetes, หรือเครื่องเซิร์ฟเวอร์ที่ติดตั้ง Jenkins

> ภาษา Groovy ที่ใช้ใน Jenkinsfile สามารถใช้ได้ทั้งแบบ Declarative และ Scripted Pipeline ขึ้นอยู่กับความต้องการและความซับซ้อนของกระบวนการ CI/CD ที่ต้องการสร้าง

> Jenkinsfile สามารถเก็บไว้ในระบบควบคุมเวอร์ชัน เช่น GitHub เพื่อให้สามารถจัดการและติดตามการเปลี่ยนแปลงได้ง่ายขึ้น

## ตัวอย่างการสร้าง Jenkinsfile แบบ Scripted Pipeline

```groovy
node('built-in') { // ระบุ agent ที่ต้องการใช้เป็น built-in
    stage('Build') {
        echo 'Building...'
        // คำสั่งสำหรับการ build เช่น การ compile โค้ด
    }
    stage('Test') {
        echo 'Testing...'
        // คำสั่งสำหรับการทดสอบ เช่น การรัน unit tests
    }
    stage('Deploy') {
        echo 'Deploying...'
        // คำสั่งสำหรับการ deploy เช่น การส่งโค้ดไปยังเซิร์ฟเวอร์
    }
}
```
อธิบายโค้ด:
- `node { ... }`: กำหนดว่าเป็น Scripted Pipeline
- `stage('Build') { ... }`: กำหนดขั้นตอน "Build"
- `echo 'Building...'`: คำสั่งสำหรับการแสดงข้อความใน Console Output
- คำสั่งสำหรับการ build, test, และ deploy สามารถปรับเปลี่ยนได้ตามความต้องการ

> แบบ Scripted Pipeline มีความยืดหยุ่นมากกว่า Declarative Pipeline แต่โค้ดอาจจะซับซ้อนและอ่านยากกว่า
> กำหนด agent ได้โดยใช้คำสั่ง `node('label') { ... }` เพื่อระบุ agent ที่ต้องการใช้
> built-in คือ label ของ agent ที่ต้องการใช้ในการรัน Pipeline

#### วิธีการตรวจสอบ Available Nodes/Agents:
1. ไปที่หน้า Jenkins Dashboard
2. คลิกที่ "Manage Jenkins"
3. คลิกที่ "Manage Nodes and Clouds"
4. จะเห็นรายการ Nodes/Agents ที่มีอยู่ในระบบ พร้อมกับ Labels ที่สามารถใช้ในการระบุ agent ใน Jenkinsfile

## การเลือกใช้ Declarative หรือ Scripted Pipeline
- Declarative Pipeline เหมาะสำหรับผู้เริ่มต้นและการสร้าง Pipeline ที่ไม่ซับซ้อน
- Scripted Pipeline เหมาะสำหรับผู้ที่มีประสบการณ์และต้องการความยืดหยุ่นมากขึ้นในการเขียนโค้ด
- สามารถผสมผสานการใช้ทั้งสองแบบในโปรเจคเดียวกันได้ตามความเหมาะสม
---

## การเชื่อมต่อ Jenkins กับ GitHub

### 1. ติดตั้ง GitHub Plugin ใน Jenkins

#### ขั้นตอนที่ 1.1: เข้าสู่ Jenkins Dashboard
- เปิดเบราว์เซอร์และไปที่ `http://localhost:8080` (หรือ URL ของ Jenkins ของคุณ)
- เข้าสู่ระบบด้วยข้อมูลที่ตั้งไว้

#### ขั้นตอนที่ 1.2: เข้าไปยัง Plugin Manager
- คลิกที่ **"Manage Jenkins"** ในเมนูด้านซ้าย
- เลือก **"Manage Plugins"** (หรือ **"Plugins"** ใน Jenkins เวอร์ชันใหม่)

#### ขั้นตอนที่ 1.3: ค้นหาและติดตั้ง GitHub Plugin
- ไปที่แท็บ **"Available plugins"**
- ค้นหา **"GitHub"** ในช่องค้นหา
- เลือก plugin ดังนี้:
  - ☑️ **GitHub Plugin** (หลัก)
  - ☑️ **GitHub Branch Source Plugin** (สำหรับ Multi-branch Pipeline)
  - ☑️ **GitHub API Plugin** (dependency)
- คลิก **"Install without restart"** หรือ **"Download now and install after restart"**

#### ขั้นตอนที่ 1.4: รอการติดตั้งเสร็จสิ้น
- รอจนกว่า plugin จะติดตั้งเสร็จ
- หากเลือก "Install without restart" อาจต้อง restart Jenkins เพื่อให้ plugin ทำงาน

### 2. สร้าง Personal Access Token (PAT) ใน GitHub

#### ขั้นตอนที่ 2.1: เข้าสู่ GitHub
- เปิด [github.com](https://github.com) และเข้าสู่ระบบ
- คลิกที่รูปโปรไฟล์ขวาบน

#### ขั้นตอนที่ 2.2: เข้าไปยัง Developer Settings
- คลิก **"Settings"** จากเมนูแบบเลื่อนลง
- เลื่อนลงไปด้านล่างในเมนูซ้าย
- คลิก **"Developer settings"**

#### ขั้นตอนที่ 2.3: สร้าง Personal Access Token
- คลิก **"Personal access tokens"**
- เลือก **"Tokens (classic)"** หรือ **"Fine-grained tokens"** (แนะนำ classic สำหรับ Jenkins)
- คลิก **"Generate new token"** → **"Generate new token (classic)"**

#### ขั้นตอนที่ 2.4: กำหนดค่า Token
**Note (ชื่อ Token):** `Jenkins Integration`

**Expiration:** เลือกอายุการใช้งาน (แนะนำ 90 days หรือ No expiration สำหรับ lab)

**Select scopes:** เลือกสิทธิ์ที่จำเป็น
- ☑️ **repo** (Full control of private repositories)
  - ☑️ repo:status
  - ☑️ repo_deployment
  - ☑️ public_repo
- ☑️ **admin:repo_hook** (Full control of repository hooks)
  - ☑️ write:repo_hook
  - ☑️ read:repo_hook
- ☑️ **user:email** (Access user email addresses)

#### ขั้นตอนที่ 2.5: สร้างและเก็บ Token
- คลิก **"Generate token"**
- **⚠️ สำคัญ:** คัดลอก token ที่ได้ทันที (จะแสดงเพียงครั้งเดียว)
- เก็บ token ไว้ในที่ปลอดภัย เช่น:
  ```
  ghp_1234567890abcdefghijklmnopqrstuvwxyz
  ```

### 3. เพิ่ม Credentials ใน Jenkins โดยใช้ PAT ที่สร้างขึ้น

#### ขั้นตอนที่ 3.1: เข้าไปยัง Credentials Manager
- กลับไปที่ Jenkins Dashboard
- คลิก **"Manage Jenkins"**
- เลือก **"Manage Credentials"**

#### ขั้นตอนที่ 3.2: เลือก Domain
- คลิกที่ **"(global)"** domain
- หรือ **"Global credentials (unrestricted)"**

#### ขั้นตอนที่ 3.3: เพิ่ม Credential ใหม่
- คลิก **"Add Credentials"** ที่มุมซ้าย

#### ขั้นตอนที่ 3.4: กำหนดค่า Credential
**Kind:** `Username with password`

**Scope:** `Global (Jenkins, nodes, items, all child items, etc)`

**Username:** ใส่ username ของ GitHub ของคุณ
```
your-github-username
```

**Password:** วาง Personal Access Token ที่สร้างไว้
```
ghp_1234567890abcdefghijklmnopqrstuvwxyz
```

**ID:** ตั้งชื่อที่จดจำง่าย (เว้นว่างไว้ก็ได้)
```
github-pat-credentials
```

**Description:** คำอธิบาย
```
GitHub Personal Access Token for Jenkins Integration
```

#### ขั้นตอนที่ 3.5: บันทึกการตั้งค่า
- คลิก **"Create"**
- จะเห็น credential ใหม่ในรายการ

### 4. ทดสอบการเชื่อมต่อ

#### ขั้นตอนที่ 4.1: สร้าง Test Job
- กลับไปที่ Jenkins Dashboard
- คลิก **"New Item"**
- ตั้งชื่อ: `test-github-connection`
- เลือก **"Freestyle project"**
- คลิก **"OK"**

#### ขั้นตอนที่ 4.2: กำหนดค่า Source Code Management
- ในหน้า Configure
- ไปที่ section **"Source Code Management"**
- เลือก **"Git"**

**Repository URL:** ใส่ URL ของ repo GitHub
```
https://github.com/your-username/your-repo.git
```

**Credentials:** เลือก credential ที่สร้างไว้
```
your-github-username/****** (GitHub Personal Access Token for Jenkins Integration)
```

#### ขั้นตอนที่ 4.3: ทดสอบการเชื่อมต่อ
- คลิก **"Save"**
- คลิก **"Build Now"**
- ดู Console Output เพื่อตรวจสอบว่าการเชื่อมต่อสำเร็จ

### 5. การแก้ไขปัญหาที่พบบ่อย

#### ปัญหา: "Authentication failed"
**สาเหตุ:** PAT หมดอายุหรือสิทธิ์ไม่เพียงพอ
**แก้ไข:** สร้าง PAT ใหม่หรือตรวจสอบ scopes

#### ปัญหา: "Repository not found"
**สาเหตุ:** URL ผิดหรือไม่มีสิทธิ์เข้าถึง repo
**แก้ไข:** ตรวจสอบ URL และสิทธิ์ของ repo

#### ปัญหา: "SSL certificate problem"
**สาเหตุ:** ปัญหา SSL certificate
**แก้ไข:** ตั้งค่า Git global config:
```bash
git config --global http.sslverify false
```

### 6. ข้อแนะนำด้านความปลอดภัย

#### การจัดการ PAT อย่างปลอดภัย
- 🔐 ใช้อายุการใช้งานที่เหมาะสม (90-180 วัน)
- 🔐 ให้สิทธิ์เฉพาะที่จำเป็นเท่านั้น
- 🔐 เก็บ token ในที่ปลอดภัย
- 🔐 หมุนเวียน token เป็นระยะ
- 🔐 ลบ token ที่ไม่ใช้งานแล้ว

#### การตั้งค่า Jenkins Security
- 🛡️ ใช้ HTTPS สำหรับ Jenkins (production)
- 🛡️ ตั้งค่า user authentication
- 🛡️ จำกัดสิทธิ์การเข้าถึง credentials
- 🛡️ ทำการสำรองข้อมูล credentials เป็นระยะ

---
## การทำงานกับ Jenkinsfile

![Jenkinsfile](https://intellij-support.jetbrains.com/hc/user_images/7IueXvigXEGikkwJ-RGd0A.png)

> Jenkinsfile คือไฟล์ที่ใช้ในการกำหนดขั้นตอนการทำงานของ Jenkins Pipeline โดยใช้ภาษา Groovy ซึ่งช่วยให้สามารถจัดการกระบวนการ CI/CD ได้อย่างมีประสิทธิภาพ

### 1. โครงสร้างพื้นฐานของ Jenkinsfile

```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
            }
        }
    }
}
```

### ตัวอย่าง Jenkinsfile สำหรับโปรเจ็กต์ทดสอบใน github ก่อนหน้าที่สร้างไว้

```groovy
pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                echo "Checking out code..."
                checkout scm // ดึงโค้ดจาก repository ที่เชื่อมต่อกับ Jenkins Job
            }
        }
        stage('Build') {
            steps {
                echo 'Building...'
                // คำสั่งสำหรับการ build เช่น การ compile โค้ด
            }
        }
    }
}
```

### 2. การใช้งาน Jenkinsfile ใน Jenkins Job
1. สร้าง Jenkins Job ใหม่
2. เลือกประเภทของ Job เป็น "Pipeline"
3. ในส่วนของ Pipeline Definition เลือก "Pipeline script from SCM"
4. เลือก "Git" เป็น SCM
5. ใส่ Repository URL และเลือก Credentials ที่สร้างไว้
6. กำหนด Branch ที่ต้องการใช้ (เช่น `main` หรือ `master`)
7. กำหนด Script Path เป็น `Jenkinsfile` (หรือชื่อไฟล์อื่น ๆ ที่ใช้)
8. บันทึกการตั้งค่าและรัน Job

## Workshop Express Docker Application

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

1.4 กำหนดค่า TypeScript
```bash
npx tsc --init
```

1.5 แก้ไข `tsconfig.json`
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

1.6 สร้างโฟลเดอร์ `src` และไฟล์ `app.ts`
```bash
mkdir src
touch src/app.ts
```

1.7 เพิ่มโค้ดตัวอย่างใน `src/app.ts`
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

1.8 สร้าง tests folder และไฟล์ `app.test.ts`
```bash
mkdir tests
touch tests/app.test.ts
```

1.9 เพิ่มโค้ดตัวอย่างใน `tests/app.test.ts`
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
1.10 กำหนดค่า Jest โดยสร้างไฟล์ `jest.config.js`
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
1.11 สร้างไฟล์ `.gitignore`
```bash
touch .gitignore
```
1.12 เพิ่มโค้ดใน `.gitignore`
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
  
    // ใช้ any agent เพื่อหลีกเลี่ยงปัญหา Docker path mounting บน Windows
    agent any

    // กำหนด environment variables
    environment {
        // ใช้ค่าเป็น "credentialsId" ของ Jenkins โดยตรงสำหรับ docker.withRegistry
        DOCKER_HUB_CREDENTIALS_ID = 'dockerhub-cred'
        DOCKER_REPO = "iamsamitdev/express-docker-app"
        APP_NAME = "express-docker-app"
    }

    // กำหนด stages ของ Pipeline
    stages {

        // Stage 1: ดึงโค้ดล่าสุดจาก Git
        stage('Checkout') {
            steps {
                echo "Checking out code..."
                checkout scm
            }
        }

        // Stage 2: ติดตั้ง dependencies และรันเทสต์ (รองรับทุก Platform)
        stage('Install & Test') {
            steps {
                script {
                    // ตรวจสอบว่ามี Node.js บน host หรือไม่
                    def hasNodeJS = false
                    def isWindows = isUnix() ? false : true
                    
                    try {
                        if (isWindows) {
                            bat 'node --version && npm --version'
                        } else {
                            sh 'node --version && npm --version'
                        }
                        hasNodeJS = true
                        echo "Using Node.js installed on ${isWindows ? 'Windows' : 'Unix'}"
                    } catch (Exception e) {
                        echo "Node.js not found on host, using Docker"
                        hasNodeJS = false
                    }
                    
                    if (hasNodeJS) {
                        // ใช้ Node.js บน host
                        if (isWindows) {
                            bat '''
                                npm install
                                npm test
                            '''
                        } else {
                            sh '''
                                npm install
                                npm test
                            '''
                        }
                    } else {
                        // ใช้ Docker run command (รองรับทุก platform)
                        if (isWindows) {
                            bat '''
                                docker run --rm ^
                                -v "%cd%":/workspace ^
                                -w /workspace ^
                                node:22-alpine sh -c "npm install && npm test"
                            '''
                        } else {
                            sh '''
                                docker run --rm \\
                                -v "$(pwd)":/workspace \\
                                -w /workspace \\
                                node:22-alpine sh -c "npm install && npm test"
                            '''
                        }
                    }
                }
            }
        }

        // Stage 3: สร้าง Docker Image สำหรับ production
        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image: ${DOCKER_REPO}:${BUILD_NUMBER}"
                    docker.build("${DOCKER_REPO}:${BUILD_NUMBER}", "--target production .")
                }
            }
        }

        // Stage 4: Push Image ไปยัง Docker Hub
        stage('Push Docker Image') {
            steps {
                script {
                    // ต้องส่งค่าเป็น credentialsId เท่านั้น ไม่ใช่ค่าที่ mask ของ credentials()
                    docker.withRegistry('https://index.docker.io/v1/', env.DOCKER_HUB_CREDENTIALS_ID) {
                        echo "Pushing image to Docker Hub..."
                        def image = docker.image("${DOCKER_REPO}:${BUILD_NUMBER}")
                        image.push()
                        image.push('latest')
                    }
                }
            }
        }

        // Stage 5: เคลียร์ Docker images และ cache บน agent
        stage('Cleanup Docker') {
            steps {
                script {
                    def isWindows = isUnix() ? false : true
                    echo "Cleaning up local Docker images/cache on agent..."
                    if (isWindows) {
                        bat """
                            docker image rm -f ${DOCKER_REPO}:${BUILD_NUMBER} || echo ignore
                            docker image rm -f ${DOCKER_REPO}:latest || echo ignore
                            docker image prune -af -f
                            docker builder prune -af -f
                        """
                    } else {
                        sh """
                            docker image rm -f ${DOCKER_REPO}:${BUILD_NUMBER} || true
                            docker image rm -f ${DOCKER_REPO}:latest || true
                            docker image prune -af -f
                            docker builder prune -af -f
                        """
                    }
                }
            }
        }

        // Stage 6: Deploy ไปยังเครื่อง local (รองรับทุก Platform)
        stage('Deploy Local') {
            steps {
                script {
                    def isWindows = isUnix() ? false : true
                    echo "Deploying container ${APP_NAME} from latest image..."
                    if (isWindows) {
                        bat """
                            docker pull ${DOCKER_REPO}:latest
                            docker stop ${APP_NAME} || echo ignore
                            docker rm ${APP_NAME} || echo ignore
                            docker run -d --name ${APP_NAME} -p 3000:3000 ${DOCKER_REPO}:latest
                            docker ps --filter name=${APP_NAME} --format \"table {{.Names}}\t{{.Image}}\t{{.Status}}\"
                        """
                    } else {
                        sh """
                            docker pull ${DOCKER_REPO}:latest
                            docker stop ${APP_NAME} || true
                            docker rm ${APP_NAME} || true
                            docker run -d --name ${APP_NAME} -p 3000:3000 ${DOCKER_REPO}:latest
                            docker ps --filter name=${APP_NAME} --format "table {{.Names}}\t{{.Image}}\t{{.Status}}"
                        """
                    }
                }
            }
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

#### 6. การตั้งค่า Webhook ใน GitHub
6.1 ไปที่หน้า GitHub Repository ของคุณ
6.2 คลิกที่ "Settings" > "Webhooks" > "Add webhook"
6.3 ในช่อง "Payload URL" ให้ใส่ URL ของ Jenkins Server ตามด้วย `/github-webhook/` (เช่น `http://your-jenkins-url/github-webhook/`)
6.4 เลือก "Content type" เป็น `application/json`
6.5 เลือก "Just the push event."
6.6 คลิก "Add webhook"

---

## สิ่งที่เรียนรู้ใน Day 4

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