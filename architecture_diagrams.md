# 🏗️ Arquitetura do Sistema DocVita

Este documento contém os diagramas da arquitetura do backend e frontend do sistema DocVita.

## Backend Architecture (Node.js/Express)

### Visão Geral da Arquitetura

```mermaid
graph TB
    subgraph "Clients"
        WEB["Flutter Web"]
        MOBILE["Flutter Mobile"]
    end

    subgraph "API Layer"
        EXPRESS["Express Server<br/>Port 3200"]
        MIDDLEWARE["CORS, Helmet, Morgan"]
    end

    subgraph "Business Logic"
        CONTROLLERS["Controllers"]
        SERVICES["Services"]
    end

    subgraph "Data & External"
        DATABASE[("MariaDB")]
        STORAGE[("Firebase Storage")]
        AI["OpenAI"]
        PAYMENTS["Payment Providers"]
    end

    WEB --> EXPRESS
    MOBILE --> EXPRESS
    EXPRESS --> MIDDLEWARE
    EXPRESS --> CONTROLLERS
    CONTROLLERS --> SERVICES
    SERVICES --> DATABASE
    SERVICES --> STORAGE
    SERVICES --> AI
    SERVICES --> PAYMENTS
```

### Detalhamento das Camadas

```mermaid
graph LR
    subgraph "Route Layer"
        AUTH["/api/auth"]
        USERS["/api/users"]
        DOCS["/api/documentos"]
        EXAMS["/api/exames"]
        MEDS["/api/medicamentos"]
        PLANS["/api/plans"]
        PAYMENTS["/api/payments"]
        SHARES["/api/shares"]
    end

    subgraph "Controller Layer"
        AUTH_CTRL["AuthController"]
        USER_CTRL["UserController"]
        DOC_CTRL["DocumentController"]
        EXAM_CTRL["ExamController"]
        MED_CTRL["MedicineController"]
        PLAN_CTRL["PlanController"]
        PAY_CTRL["PaymentController"]
        SHARE_CTRL["ShareController"]
    end

    subgraph "Service Layer"
        DOC_SVC["DocumentService"]
        PAY_SVC["PaymentService"]
        PLAN_SVC["PlanService"]
        SHARE_SVC["ShareService"]
        UPLOAD_SVC["UploadService"]
    end

    AUTH --> AUTH_CTRL
    USERS --> USER_CTRL
    DOCS --> DOC_CTRL
    EXAMS --> EXAM_CTRL
    MEDS --> MED_CTRL
    PLANS --> PLAN_CTRL
    PAYMENTS --> PAY_CTRL
    SHARES --> SHARE_CTRL

    DOC_CTRL --> DOC_SVC
    PAY_CTRL --> PAY_SVC
    PLAN_CTRL --> PLAN_SVC
    SHARE_CTRL --> SHARE_SVC
    DOC_CTRL --> UPLOAD_SVC
```

### Integrações Externas

```mermaid
graph TB
    subgraph "Backend Services"
        API["Express API"]
    end

    subgraph "Authentication"
        JWT["JWT Tokens"]
        FIREBASE_AUTH["Firebase Auth"]
    end

    subgraph "AI & Analysis"
        OPENAI["OpenAI API"]
        DOC_ANALYSIS["Document Analysis"]
        FORM_PREFILL["Form Prefill"]
    end

    subgraph "Payments"
        ASAAS["Asaas"]
        STRIPE["Stripe"]
        PAYMENT_FACTORY["Payment Factory"]
    end

    subgraph "Storage"
        FIREBASE_STORAGE["Firebase Storage"]
        FILE_SYSTEM["Local Files"]
    end

    subgraph "Database"
        MARIADB[("MariaDB")]
    end

    API --> JWT
    API --> FIREBASE_AUTH
    API --> OPENAI
    OPENAI --> DOC_ANALYSIS
    OPENAI --> FORM_PREFILL
    API --> PAYMENT_FACTORY
    PAYMENT_FACTORY --> ASAAS
    PAYMENT_FACTORY --> STRIPE
    API --> FIREBASE_STORAGE
    API --> FILE_SYSTEM
    API --> MARIADB
```

## Frontend Architecture (Flutter)

### Visão Geral da Arquitetura

```mermaid
graph TB
    subgraph "Platforms"
        WEB["Flutter Web"]
        ANDROID["Android"]
        IOS["iOS"]
    end

    subgraph "App Structure"
        MAIN["main.dart"]
        ROUTES["Routes"]
        THEME["Theme"]
    end

    subgraph "Feature Modules"
        FEATURES["Feature Modules<br/>(Auth, Docs, Exams, etc.)"]
    end

    subgraph "Core Layer"
        SERVICES["Core Services"]
        MODELS["Core Models"]
        WIDGETS["Core Widgets"]
    end

    WEB --> MAIN
    ANDROID --> MAIN
    IOS --> MAIN
    MAIN --> ROUTES
    MAIN --> THEME
    MAIN --> FEATURES
    MAIN --> SERVICES
    FEATURES --> MODELS
    FEATURES --> WIDGETS
```

### Estrutura dos Features

```mermaid
graph LR
    subgraph "Feature Modules"
        AUTH["Auth Feature"]
        DOCS["Documents Feature"]
        EXAMS["Exams Feature"]
        MEDS["Medicines Feature"]
        PLANS["Plans Feature"]
        PROFILE["Profile Feature"]
        VACCINES["Vaccines Feature"]
        WEB_LANDING["Web Landing"]
    end

    subgraph "Feature Structure"
        UI["UI Layer"]
        DATA["Data Layer"]
        DOMAIN["Domain Layer"]
    end

    AUTH --> UI
    DOCS --> UI
    EXAMS --> UI
    MEDS --> UI
    PLANS --> UI
    PROFILE --> UI
    VACCINES --> UI
    WEB_LANDING --> UI

    UI --> DATA
    DATA --> DOMAIN
```

### Core Services e Integrações

```mermaid
graph TB
    subgraph "Core Services"
        API_SERVICE["API Service"]
        AUTH_SERVICE["Auth Service"]
        FILE_SERVICE["File Service"]
        NOTIFICATION_SERVICE["Notification Service"]
        SESSION_SERVICE["Session Service"]
        BIOMETRIC_SERVICE["Biometric Service"]
    end

    subgraph "External Integrations"
        FIREBASE["Firebase SDK"]
        GOOGLE_SIGNIN["Google Sign-In"]
        ML_KIT["ML Kit OCR"]
        PDF_VIEWER["PDF Viewer"]
        IMAGE_PICKER["Image Picker"]
        FILE_PICKER["File Picker"]
    end

    subgraph "State Management"
        PROVIDER["Provider Pattern"]
        SESSION_MANAGER["Session Manager"]
        PLAN_LIMITER["Plan Limiter"]
    end

    API_SERVICE --> FIREBASE
    AUTH_SERVICE --> GOOGLE_SIGNIN
    FILE_SERVICE --> ML_KIT
    FILE_SERVICE --> PDF_VIEWER
    FILE_SERVICE --> IMAGE_PICKER
    FILE_SERVICE --> FILE_PICKER

    API_SERVICE --> PROVIDER
    SESSION_SERVICE --> SESSION_MANAGER
    API_SERVICE --> PLAN_LIMITER
```

### Fluxo de Navegação

```mermaid
graph TD
    START["App Start"]
    LOADING["Loading Screen"]
    AUTH_CHECK{"User Authenticated?"}
    LOGIN["Login Page"]
    HOME["Home Page"]
    
    subgraph "Main Features"
        DOCS_PAGE["Documents Page"]
        EXAMS_PAGE["Exams Page"]
        MEDS_PAGE["Medicines Page"]
        PLANS_PAGE["Plans Page"]
        PROFILE_PAGE["Profile Page"]
    end

    START --> LOADING
    LOADING --> AUTH_CHECK
    AUTH_CHECK -->|No| LOGIN
    AUTH_CHECK -->|Yes| HOME
    LOGIN --> HOME
    HOME --> DOCS_PAGE
    HOME --> EXAMS_PAGE
    HOME --> MEDS_PAGE
    HOME --> PLANS_PAGE
    HOME --> PROFILE_PAGE
```

## Data Flow Sequence

```mermaid
sequenceDiagram
    participant U as User (Flutter)
    participant F as Flutter App
    participant B as Backend API
    participant DB as MariaDB
    participant FS as Firebase Storage
    participant AI as OpenAI

    U->>F: Login Request
    F->>B: POST /api/auth/login
    B->>DB: Validate Credentials
    DB-->>B: User Data
    B-->>F: JWT Token
    F-->>U: Login Success

    U->>F: Upload Document
    F->>B: POST /api/documentos
    B->>FS: Upload File
    FS-->>B: File URL
    B->>DB: Save Document Record
    DB-->>B: Document ID
    B-->>F: Document Created

    U->>F: Analyze Document
    F->>B: POST /api/openai/analyze
    B->>AI: Send Document Content
    AI-->>B: Analysis Result
    B->>DB: Save Analysis
    DB-->>B: Analysis ID
    B-->>F: Analysis Complete

    U->>F: Share Document
    F->>B: POST /api/shares
    B->>DB: Create Share Token
    DB-->>B: Share Token
    B-->>F: Share URL
    F-->>U: Share Link Generated
```

## 📋 Arquitetura Resumida

### Backend (Node.js/Express)
- **Padrão**: MVC com camadas bem definidas
- **Banco**: MariaDB com pool de conexões
- **Auth**: JWT + Firebase Auth
- **Pagamentos**: Asaas e Stripe
- **IA**: OpenAI para análise de documentos
- **Storage**: Firebase Storage + sistema local

### Frontend (Flutter)
- **Padrão**: Feature-based com Clean Architecture
- **Estado**: Provider pattern
- **Plataformas**: Web, Android, iOS
- **Integrações**: Firebase, Google Sign-In, ML Kit

### Principais Características
1. **Sistema de Planos**: Limites por plano
2. **Compartilhamento**: Tokens únicos para documentos
3. **IA**: Análise automática de documentos médicos
4. **Multi-plataforma**: Web e mobile
5. **Pagamentos**: Múltiplos provedores
6. **Segurança**: Autenticação robusta
