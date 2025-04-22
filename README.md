# 🚀 Nexlayer Deployment YAML: Developer's Quick Start Guide

Hello developers! Welcome to the Nexlayer deployment YAML guide that gets you from zero to deployed in minutes. Whether you're a freelancer, indie developer, creator, or startup founder, this guide will help you deploy lightning-fast on Nexlayer AI Cloud.

## 📋 Table of Contents

1. [What is Nexlayer?](#-what-is-nexlayer)
2. [Quick Start: Deploy in 5 Minutes](#-quick-start-deploy-in-5-minutes)
3. [YAML Building Blocks](#-yaml-building-blocks)
4. [Image Management](#-image-management)
5. [Visual Diagrams](#-visual-diagrams)
6. [Common App Patterns](#-common-app-patterns)
7. [Cheat Sheet: Pod Configuration](#-cheat-sheet-pod-configuration)
8. [How Pods Talk to Each Other](#-how-pods-talk-to-each-other)
9. [Storing Data with Volumes](#-storing-data-with-volumes)
10. [Keeping Secrets Safe](#-keeping-secrets-safe)
11. [Using Private Images](#-using-private-images)
12. [Common Mistakes to Avoid](#-common-mistakes-to-avoid)
13. [Full Example: Gaming Leaderboard App](#-full-example-gaming-leaderboard-app)
14. [Real-World Use Cases](#-real-world-use-cases)
15. [Pro Tips](#-pro-tips)
16. [Next Steps](#-next-steps)
17. [Detailed Schema Reference](#-detailed-schema-reference)
18. [Important Distinctions](#-important-distinctions)

## 🦾 ☁ What is Nexlayer?

Nexlayer is an AI-powered cloud built for developers who want to ship faster, scale effortlessly, and skip the DevOps headaches.

Define your app's structure in a simple YAML file, and Nexlayer automates everything—provisioning, scaling, networking, and security—so you can focus on building, not configuring. No Kubernetes wrangling, no complex infra setup.

Unlike legacy platforms, Nexlayer is AI-native and designed for modern apps, AI models, and scalable backends—without vendor lock-in or unnecessary complexity. Write YAML, deploy, and go.

## ⚡️ Why Nexlayer?

- ✅ Zero DevOps – Write YAML, deploy, done.
- ✅ Auto-Scaling – Handles traffic spikes automatically.
- ✅ Built-in Security – Secrets management & encrypted storage.
- ✅ AI & ML Ready – Deploy AI models with zero friction.
- ✅ Effortless Networking – Services auto-discover, no networking configs.
- ✅ Simple Deployments – No infra setup
- ✅ Stack-Agnostic – Works with APIs, web apps, AI services, and more.

🚀 Less setup, more shipping.

## 🔥 Quick Start: Deploy in 5 Minutes

Let's get your first app running on Nexlayer right now:

### Step 1: Create a file named `nexlayer.yaml`

### Step 2: Copy this starter template

```yaml
application: # The name of the deployment
  name: "my-first-app" # Required: Globally unique application identifier
  # url: "www.example.ai"  # Optional: Include only for permanent deployments
  pods: # Required: List of containers
    - name: webapp # Required: Must start with lowercase letter, use only alphanumeric, hyphens, or dots
      image: "your-username/my-app:v1.2.0" # Required: Docker image (must be hosted on registry)
      path: / # Optional: URL path where the service is accessible (only required for web-facing pods)
      servicePorts: # Required: List of ports exposed by this pod
        - 80 # Format: Simple list of integers
```

**💡 Tip**: If you prefer a more interactive way to create your `nexlayer.yaml`, try our **[Deployment Template Builder](https://app.nexlayer.io/#/nexlayer-deployment-wizard)**. It lets you visually configure your application and generates the YAML for you—no manual coding needed!

### Step 3: Deploy it!

That's it! You just deployed a web service to Nexlayer. Let's understand what you did...

## 🧩 YAML Building Blocks

Nexlayer YAML has a simple structure:

```
application
├── name: Your app's name
├── url: Your app's URL (optional)
├── registryLogin (optional for private images)
└── pods: List of containers
    ├── Pod 1 (like a web server)
    │   ├── name: pod name
    │   ├── image: container image
    │   ├── path: web route
    │   ├── servicePorts: exposed ports
    │   │   └── - port number
    │   ├── vars: environment variables
    │   │   ├── ENV_VAR1: value1
    │   │   └── ENV_VAR2: value2
    │   ├── volumes: persistent storage
    │   │   └── - name: volume name
    │   │       ├── size: storage size
    │   │       └── mountPath: storage location
    │   └── secrets: sensitive data
    │       └── - name: secret name
    │           ├── data: secret content
    │           ├── mountPath: secret location
    │           └── fileName: secret file name
    │
    ├── Pod 2 (like a database)
    │   └── ...
    └── Pod 3 (like a cache)
        └── ...
```

Each pod is a container that runs a specific part of your application. They automatically talk to each other!

## 🖼️ Image Management

Nexlayer requires all Docker images to be hosted on a registry—local images aren't supported since it's a cloud platform.

### Public Images on Docker Hub

Use your own public image with the format your-username/my-app:<tag>:

```yaml
application:
  pods:
    - name: "app"
      image: "your-username/my-app:v1.2.0" # Your public image on Docker Hub
```

If you omit the tag (e.g., your-username/my-app), Docker Hub defaults to :latest.

Note: Generic images like nginx:latest work locally but aren't suitable for your app on Nexlayer—push your own image instead.

### Public Images on GHCR.io

Use ghcr.io/your-username/my-app:<tag> for public images on GitHub Container Registry:

```yaml
application:
  pods:
    - name: "app"
      image: "ghcr.io/your-username/my-app:v1.2.0" # Your public image on GHCR.io
```

Without a tag (e.g., ghcr.io/your-username/my-app), it defaults to :latest.

### Private Images

For private images on any registry (e.g., GHCR.io or Docker Hub), use <% REGISTRY %> with authentication:

```yaml
application:
  registryLogin:
    registry: "ghcr.io" # Registry hostname (e.g., ghcr.io, docker.io)
    username: "your-username" # Registry username - case sensitive!
    personalAccessToken: "your-token" # Registry access token/password
  pods:
    - name: "app"
      image: "<% REGISTRY %>/your-username/my-app:v1.2.0" # Private image
```

Omitting the tag (e.g., <% REGISTRY %>/your-username/my-app) defaults to :latest.

Tip: Specify tags (e.g., v1.2.0) for consistency; :latest might pull unexpected updates.

## 📊 Visual Diagrams

### Pod Interactions Flowchart

Here's how pods connect to each other in a typical fullstack application:

```mermaid
graph TD
    subgraph NexlayerCloud["Nexlayer AI Cloud Cluster"]
        %% Frontend app
        Frontend[Next.js Frontend<br>path: '/'<br>Port: 3000]

        %% Backend app
        Backend[FastAPI Backend<br>path: '/api'<br>Port: 8000]

        %% Databases
        DB[(PostgreSQL<br>Port: 5432)]
        VectorDB[(Pinecone Vector DB<br>Port: 8080)]
    end

    %% External entities
    ExternalAPI[OpenAI API]

    %% Relationships
    Frontend -->|fastapi.pod:8000| Backend
    Backend -->|postgresql://postgres.pod:5432/mydb| DB
    Backend -->|pinecone.pod:8080| VectorDB
    Backend -->|API calls| ExternalAPI

    %% Styling
    classDef app fill:#ACFFFC,color:black,stroke:#ccc,stroke-width:2px
    classDef data fill:#EF8CA4,color:black,stroke:#ccc,stroke-width:2px
    classDef external fill:#cccccc,color:black,stroke:#999,stroke-width:2px
    classDef nexlayer fill:#f2f2f2,stroke:#e0e0e0,stroke-width:1px

    class Frontend,Backend app
    class DB,VectorDB data
    class ExternalAPI external
    class NexlayerCloud nexlayer
```

This diagram shows how Nexlayer's automatic service discovery works:

- The Next.js frontend connects to the FastAPI backend using fastapi.pod:8000
- The FastAPI backend connects to PostgreSQL using postgres.pod:5432
- The FastAPI backend also connects to Pinecone vector database using pinecone.pod:8080
- The FastAPI backend connects to external OpenAI API (external services work normally)

Each pod can reference other pods using the <pod-name>.pod syntax without worrying about IP addresses.

### YAML Structure Map

This map shows the hierarchical structure of a Nexlayer YAML file for an AI-powered application:

```
application
├── name: "ai-powered-app"
├── url: "https://myai.example.com" (optional)
├── registryLogin (optional)
│   ├── registry: "registry.example.com"
│   ├── username: "myuser"
│   └── personalAccessToken: "mypat123"
└── pods
    ├── next-frontend
    │   ├── name: "nextjs"
    │   ├── image: "vercel/next:latest"
    │   ├── path: "/"
    │   ├── servicePorts: [3000]
    │   └── vars:
    │       └── BACKEND_URL: "http://fastapi.pod:8000"
    ├── fastapi-backend
    │   ├── name: "fastapi"
    │   ├── image: "tiangolo/fastapi:latest"
    │   ├── path: "/api"
    │   ├── servicePorts: [8000]
    │   ├── vars:
    │       ├── DATABASE_URL: "postgresql://postgres:password@postgres.pod:5432/mydb"
    │       ├── PINECONE_URL: "http://pinecone.pod:8080"
    │       └── OPENAI_API_KEY: "sk-..." # Set via secrets instead for production
    │   └── secrets:
    │       └── name: "api-keys"
    │           data: "your-openai-key-here"
    │           mountPath: "/app/secrets"
    │           fileName: "openai.key"
    ├── postgres-db
    │   ├── name: "postgres"
    │   ├── image: "postgres:14"
    │   ├── servicePorts: [5432]
    │   ├── vars:
    │       ├── POSTGRES_USER: "postgres"
    │       ├── POSTGRES_PASSWORD: "password"
    │       └── POSTGRES_DB: "mydb"
    │   └── volumes:
    │       └── name: "postgres-data"
    │           size: "5Gi"
    │           mountPath: "/var/lib/postgresql/data"
    └── pinecone-vector-db
        ├── name: "pinecone"
        ├── image: "pinecone/pinecone-server:latest"
        ├── servicePorts: [8080]
        └── volumes:
            └── name: "vector-data"
                size: "10Gi"
                mountPath: "/data"
```

This visualization helps you understand how different elements of your configuration relate to each other.

## 🛠️ Common App Patterns

### 💻 Simple Website

```yaml
application:
  name: "my-website" # Required: Globally unique application name
  pods:
    - name: web # Required: Unique pod name
      image: your-username/my-app:v1.2.0 # Required: Docker image from registry
      path: / # Optional: URL route (must start with /)
      servicePorts: # Required: List of exposed ports
        - 80 # Format: Simple integer
```

### 🔄 Frontend + Backend + Database

```yaml
application:
  name: "fullstack-app"
  pods:
    - name: frontend
      image: "your-username/frontend-app:v1.0.0" # Your public image on Docker Hub
      path: /
      servicePorts:
        - 3000
      vars: # Environment variables as key-value pairs
        API_URL: "http://backend.pod:4000" # Reference other pods with .pod suffix

    - name: backend
      image: "your-username/backend-app:v1.0.0" # Your public image on Docker Hub
      path: /api # Path must start with /
      servicePorts:
        - 4000
      vars:
        DATABASE_URL: "postgresql://user:pass@database.pod:5432/mydb" # Proper inter-pod reference

    - name: database
      image: "postgres:14" # Standard database image from Docker Hub
      servicePorts:
        - 5432
      vars:
        POSTGRES_USER: "user"
        POSTGRES_PASSWORD: "pass"
        POSTGRES_DB: "mydb"
      volumes:
        - name: db-data # Unique volume name
          size: "1Gi" # Storage size with units (Mi, Gi, Ti)
          mountPath: "/var/lib/postgresql/data" # Must start with /
```

## 🧠 AI Application Template

```yaml
application:
  name: "ai-app"
  pods:
    - name: frontend
      image: "your-username/ai-frontend:v1.0.0" # Your public image on Docker Hub
      path: /
      servicePorts:
        - 3000
      vars:
        API_URL: "http://ai-backend.pod:5000" # Note .pod suffix for pod reference

    - name: ai-backend
      image: "your-username/ai-backend:v1.0.0" # Your public image on Docker Hub
      servicePorts:
        - 5000
      vars:
        MODEL_PATH: "/models" # Path starts with /
        VECTOR_DB: "http://vector-db.pod:8080" # Note .pod suffix
      volumes:
        - name: model-storage
          size: "5Gi"
          mountPath: "/models" # Path starts with /

    - name: vector-db
      image: "weaviate/weaviate:latest" # Standard vector database image
      servicePorts:
        - 8080
      volumes:
        - name: vector-data
          size: "2Gi"
          mountPath: "/data" # Path starts with /
```

## 🔍 Cheat Sheet: Pod Configuration

| Key              | Definition                                                                                                                                                                                                            | Why it matters                                                                                                                                                                                            | Examples                                                                                                                                   |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| **name**         | A unique name to identify this service.                                                                                                                                                                               | Each little machine (pod) must work correctly for your app to run—if one machine breaks, your whole app might not work and your friends wouldn't be able to use it.                                       | `name: postgres`                                                                                                                           |
| **image**        | Specifies the Docker container image (including repository info) to deploy for that pod. The image must be hosted and, for private images, follow the `<% REGISTRY %>/<...>` format.                                  | This tells Nexlayer exactly which pre-built container to use for your live app. Choosing a solid image means your app runs in a proven, ready-to-go environment for all your users.                       | `image: "postgres:latest"` or `image: "cooldb/image:1.0"`                                                                                  |
| **path**         | For web-facing pods, defines the external URL route where users access the service.                                                                                                                                   | This sets the web address path where users access your service. A well-defined path means your website, service or API is easily found, making your app look friendly and professional on Nexlayer Cloud. | `path: "/"` or `path: "/api"`                                                                                                              |
| **servicePorts** | Defines the ports for external access or inter-service communication.                                                                                                                                                 | These ports are like the doorways that let users (or other services) connect to your app. Set them correctly, and your live app will be easily accessible and reliable on the web.                        | `servicePorts: - 5432`                                                                                                                     |
| **vars**         | Runtime environment variables defined as direct key-value pairs. Use `<pod-name>.pod` to reference other pods or `<% URL %>` for the deployment's base URL.                                                           | These are the settings that tell your live app how to connect to databases, APIs, and more. When they're set up right, your app adapts perfectly to the cloud environment, keeping your users happy.      | `vars:`<br>`  POSTGRES_USER: postgres`<br>`  POSTGRES_PASSWORD: password`<br>`  POSTGRES_DB: mydb`<br>`  API_URL: http://backend.pod:3000` |
| **volumes**      | Optional persistent storage settings that ensure data isn't lost between restarts. Each volume includes a name, size, and a mountPath.                                                                                | Volumes are like cloud hard drives for your app. They store important data (like database files) so that nothing is lost when your app updates or restarts, keeping your users' data safe.                | `volumes: - name: postgres-data size: 5Gi mountPath: /var/lib/postgresql/data`                                                             |
| **mountPath**    | Within a volume configuration, specifies the internal file system location where the volume attaches. Must start with a "/".                                                                                          | This tells Nexlayer exactly where to plug in your volume within a running container. When set correctly, your live app can read and save data smoothly—ensuring a seamless user experience.               | `mountPath: "/var/lib/postgresql/data"`                                                                                                    |
| **secrets**      | Securely mount sensitive data into your app's configuration files. Each secret includes a name, data (raw text or Base64-encoded), a mountPath (must start with "/"), and a fileName to name the mounted secret file. | Secrets keep your sensitive info locked away safely. By using secrets, you protect passwords and keys while ensuring your app runs securely—giving your users peace of mind.                              | `secrets: - name: nextauth-secret data: "myrandomsecret" mountPath: "/var/secrets/nextauth" fileName: secret.txt`                          |

> **Note:** There are additional configuration options available in the schema that are managed internally by Nexlayer.

## 🔌 How Pods Talk to Each Other

The magic of Nexlayer: pods automatically discover each other! Use `<pod-name>.pod` in your configuration:

```yaml
vars:
  DATABASE_URL: "postgresql://postgres:postgres@database.pod:5432/myapp" # CORRECT: Using .pod suffix
  API_URL: "http://api.pod:8000" # References another pod named "api"
  SITE_URL: "<% URL %>/dashboard" # References the deployment's base URL
```

You can use:

- `<pod-name>.pod` to reference other pods (required when connecting services)
- `<% URL %>` to reference the URL of your deployment site

## 💾 Storing Data with Volumes

Keep your data safe between restarts:

```yaml
volumes:
  - name: my-data # Give it a name
    size: "1Gi" # How much space (1Gi = 1 Gigabyte)
    mountPath: "/data" # Where to find it in your container (must start with /)
```

### 🧠 Do I Need to Use `mountPath` Like `/var/lib/postgresql/data`?

**Short answer: Not always.**

Nexlayer simplifies how volumes are mounted by handling common defaults internally.

If you're using a standard, publicly hosted image like `postgres`, `redis`, or `mongo`, Nexlayer **automatically mounts volumes to the correct internal paths** — so you don’t need to explicitly specify `mountPath` unless:

- ✅ You’re using a **custom image** that expects data in a specific directory
- ✅ You have **explicit logic in your app** that reads/writes from a known file path
- ✅ You're mounting **secrets** to a specific location in the filesystem

```yaml
# ✅ Optional if using a known image like postgres
volumes:
  - name: postgres-data
    size: "5Gi"
    # mountPath: "/var/lib/postgresql/data"  # Optional – Nexlayer auto-mounts for postgres
```

The Nexlayer platform abstracts path requirements for standard services. Keep your YAML cleaner by omitting mountPath unless your container specifically needs it.

## 🔐 Keeping Secrets Safe

Store API keys, passwords, and other sensitive data securely:

```yaml
secrets:
  - name: api-keys # Unique name within pod
    data: "my-super-secret-api-key" # Actual secret value
    mountPath: "/var/secrets" # Must start with /
    fileName: "api-key.txt" # Name of the file containing the secret
```

Your app can then read `/var/secrets/api-key.txt` to get the secret value.

## 🐳 Using Private Images

If your Docker images are in a private registry:

```yaml
application:
  name: "private-app"
  registryLogin: # Required for private images - registry authentication details
    registry: "ghcr.io" # Registry hostname (e.g., ghcr.io, docker.io)
    username: "your-username" # Registry username (case sensitive!)
    personalAccessToken: "my-token" # Read-only registry Personal Access Token
  pods:
    - name: private-service
      # For private images use the following schema exactly as shown:
      # Images are tagged as private if they include '<% REGISTRY %>'
      image: "<% REGISTRY %>/your-username/private-image:latest" # This gets replaced with the registry above
      servicePorts:
        - 3000
      # ... rest of config
```

Note that the username in the image path must match exactly (including case) with the username in `registryLogin`.

## 🚨 Common Mistakes to Avoid

1. ❌ **Forgetting the `application:` block at the start**  
   ✅ Always begin your YAML with `application:`

2. ❌ **Using the same pod name twice**  
   ✅ Each pod name must be unique

3. ❌ **Incorrect pod name format**  
   ✅ Pod names must start with a lowercase letter and can include only alphanumeric characters, hyphens, or dots

4. ❌ **Mixing up `path` and `mountPath`**  
   ✅ `path` is for URL routes (like `/api`), `mountPath` is for filesystem paths (like `/data`)

5. ❌ **Forgetting servicePorts**  
   ✅ Each pod needs servicePorts to be accessible

6. ❌ **Incorrect pod references**  
   ✅ Use `<pod-name>.pod` to connect services (not IP addresses)
7. ❌ **Trying to use Kubernetes or Docker Compose syntax**  
   ✅ Nexlayer has its own unique YAML schema

8. ❌ **DO NOT add `resources.limits` manually to your YAML.**  
   ✅ Nexlayer **automatically** configures CPU & Memory for each service.  
   ✅ If you add `resources.limits` manually, it will be ignored.

9. ❌ **Misunderstanding entrypoint and command behavior**  
   ✅ If entrypoint and command are explicitly defined in Docker Compose, the Nexlayer-CLI will translate them into Nexlayer YAML.  
   ✅ If they are not defined in Docker Compose, the Nexlayer-CLI omits them, defaulting to the Dockerfile's built-in values.

10. ❌ **Using array format for environment variables**  
    ✅ Use direct key-value pairs for environment variables:

    ```yaml
    vars:
      ENV_VAR_KEY: "value" # CORRECT
    ```

    ```yaml
    vars:
      - key: "ENV_VAR_KEY" # INCORRECT
        value: "value"
    ```

11. ❌ **Trying to use local Docker images**  
    ✅ All images must be hosted on a registry (Docker Hub, GHCR.io, etc.)
12. ❌ **Case mismatch between registry username and image path**  
    ✅ Ensure the username in your image path exactly matches the registry username (case sensitive)

## 🎮 Full Example: Gaming Leaderboard App

```yaml
application:
  name: "game-leaderboard" # Required: Application name
  pods:
    - name: frontend # Required: Unique pod name
      image: "your-username/game-ui:v1.0.0" # Your public image on Docker Hub
      path: "/" # URL route (must start with /)
      servicePorts: # Required: List of exposed ports
        - 3000
      vars: # Environment variables as key-value pairs
        API_URL: "http://api.pod:8080" # Note .pod suffix
        WEBSOCKET_URL: "ws://api.pod:8080/ws" # Note .pod suffix

    - name: api
      image: "your-username/game-api:v1.0.0" # Your public image on Docker Hub
      path: "/api" # Path starts with /
      servicePorts:
        - 8080
      vars:
        MONGO_URI: "mongodb://mongo.pod:27017/leaderboard" # Note .pod suffix
        REDIS_URL: "redis://redis.pod:6379" # Note .pod suffix
        JWT_SECRET: "supersecretkey"

    - name: mongo
      image: "mongo:latest" # Standard database image from Docker Hub
      servicePorts:
        - 27017
      volumes:
        - name: mongo-data
          size: "2Gi" # Storage size with units
          mountPath: "/data/db" # Must start with /

    - name: redis
      image: "redis:latest" # Standard cache image from Docker Hub
      servicePorts:
        - 6379
      volumes:
        - name: redis-data
          size: "1Gi"
          mountPath: "/data" # Must start with /
```

## 📱 Real-World Use Cases

### Social Media App

```yaml
application:
  name: "social-media"
  pods:
    - name: frontend
      image: "your-username/social-frontend:v1.0.0"
      path: "/"
      servicePorts:
        - 3000
      vars:
        API_URL: "http://api.pod:8000" # Note .pod suffix
        MEDIA_URL: "http://media.pod:9000" # Note .pod suffix

    - name: api
      image: "your-username/social-api:v1.0.0"
      path: "/api" # Path starts with /
      servicePorts:
        - 8000
      vars:
        DATABASE_URL: "postgresql://postgres:password@postgres.pod:5432/socialdb" # Note .pod suffix
        REDIS_URL: "redis://redis.pod:6379" # Note .pod suffix
        MEDIA_SERVICE: "http://media.pod:9000" # Note .pod suffix

    - name: media
      image: "your-username/media-service:v1.0.0"
      path: "/media" # Path starts with /
      servicePorts:
        - 9000
      vars:
        STORAGE_PATH: "/data/media" # Path starts with /
      volumes:
        - name: media-storage
          size: "10Gi"
          mountPath: "/data/media" # Must start with /

    - name: postgres
      image: "postgres:14"
      servicePorts:
        - 5432
      vars:
        POSTGRES_USER: "postgres"
        POSTGRES_PASSWORD: "password"
        POSTGRES_DB: "socialdb"
      volumes:
        - name: postgres-data
          size: "5Gi"
          mountPath: "/var/lib/postgresql/data" # Must start with /

    - name: redis
      image: "redis:latest"
      servicePorts:
        - 6379
      volumes:
        - name: redis-data
          size: "1Gi"
          mountPath: "/data" # Must start with /
```

### E-Commerce Platform

```yaml
application:
  name: "ecommerce"
  pods:
    - name: storefront
      image: "your-username/store-frontend:v2.1.0"
      path: "/"
      servicePorts:
        - 3000
      vars:
        API_URL: "http://api.pod:4000" # Note .pod suffix
        STRIPE_PUBLIC_KEY: "pk_test_123"

    - name: admin
      image: "your-username/admin-panel:v2.1.0"
      path: "/admin" # Path starts with /
      servicePorts:
        - 3001
      vars:
        API_URL: "http://api.pod:4000" # Note .pod suffix

    - name: api
      image: "your-username/ecommerce-api:v2.1.0"
      path: "/api" # Path starts with /
      servicePorts:
        - 4000
      vars:
        DATABASE_URL: "postgresql://postgres:password@postgres.pod:5432/shopdb" # Note .pod suffix
        REDIS_URL: "redis://redis.pod:6379" # Note .pod suffix
        ELASTICSEARCH_URL: "http://elasticsearch.pod:9200" # Note .pod suffix
      secrets:
        - name: stripe-key
          data: "sk_test_your_stripe_secret_key"
          mountPath: "/app/secrets" # Must start with /
          fileName: "stripe.key"

    - name: postgres
      image: "postgres:14"
      servicePorts:
        - 5432
      vars:
        POSTGRES_USER: "postgres"
        POSTGRES_PASSWORD: "password"
        POSTGRES_DB: "shopdb"
      volumes:
        - name: postgres-data
          size: "10Gi"
          mountPath: "/var/lib/postgresql/data" # Must start with /

    - name: redis
      image: "redis:latest"
      servicePorts:
        - 6379
      volumes:
        - name: redis-data
          size: "2Gi"
          mountPath: "/data" # Must start with /

    - name: elasticsearch
      image: "elasticsearch:8.6.0"
      servicePorts:
        - 9200
      vars:
        discovery.type: "single-node"
        ES_JAVA_OPTS: "-Xms512m -Xmx512m"
      volumes:
        - name: es-data
          size: "20Gi"
          mountPath: "/usr/share/elasticsearch/data" # Must start with /
```

## 📝 Deployment Behavior: Preview vs Production

Understanding the `url` field is important for deployment behavior:

- **Without `url` field**: Creates a temporary preview deployment (lasts ~2 hours)
- **With `url` field**: Creates a permanent deployment until deleted

```yaml
application:
  name: "my-app"
  url: "www.example.ai" # Include for permanent deployments, omit for ~2 hour previews
  # Rest of configuration...
```

No need to add the `url` key if this is not going to be a permanent deployment.

## 🚀 Next Steps

Now that you've mastered the basics, here are some advanced topics to explore:

1. **Custom Domains**: Configure your own domains for your Nexlayer applications.

2. **Advanced Networking**: Learn about creating internal-only services and managing network policies.

3. **Observability**: Set up logging, monitoring, and alerting for your applications.

4. **CI/CD Integration**: Automate your deployments with GitHub Actions or other CI/CD tools.

5. **Scaling Strategies**: Understand how to optimize your application for automatic scaling.

## 📚 Detailed Schema Reference

For a comprehensive reference of all available fields in the Nexlayer YAML schema, visit our [detailed documentation](https://docs.nexlayer.io/schema).

## ⚠️ Important Distinctions

### Nexlayer vs. Kubernetes

While Nexlayer abstracts away the complexity of Kubernetes, there are some important distinctions:

- Nexlayer YAML is simpler and more focused on application definition rather than infrastructure.
- Nexlayer handles networking, scaling, and security automatically.
- Resources are allocated dynamically rather than requiring explicit configuration.
- Service discovery is automatic with the `<pod-name>.pod` convention.

### Nexlayer vs. Docker Compose

Nexlayer's YAML format shares some similarities with Docker Compose, but has important differences:

- Nexlayer is designed for cloud deployment, not local development.
- All images must be hosted on a registry, not built or referenced locally.
- Nexlayer provides automatic service discovery and routing.
- Nexlayer handles complex networking and security automatically.

Remember, Nexlayer is designed to simplify your deployment workflow while giving you the power to build sophisticated, scalable applications without the typical infrastructure headaches. Happy deploying!
