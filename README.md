# DevOps Engineering Assignment: Real-Time Chat App

Welcome! In this assignment, you are tasked with fixing a broken staging environment for our Real-Time Chat web application. 

echo "<!-- CI/CD test comment -->" >> README.md
A junior developer recently attempted to containerize this application using Docker and NGINX, but the deployment is currently failing on multiple fronts. Your job is to debug their configuration files and get the application fully operational via Docker Compose.

## System Architecture

The application is built using two primary containers:
1. **Backend (`backend`)**: A Python-based FastAPI server operating on Port 8000. It handles persistent, real-time WebSocket connections on the `/ws` endpoint.
2. **Frontend Proxy (`nginx`)**: An NGINX container mapped to Port 80. It is responsible for serving the static files from the `frontend/` directory, while simultaneously intercepting and reverse-proxying all WebSocket upgrade requests down to the backend container.

### Directory Structure
```text
realtime-chat-app/
├── app/
│   ├── main.py              # FastAPI application server
│   └── requirements.txt     # Python dependencies
├── frontend/
│   └── index.html           # Simple, styled single-page HTML client
├── Dockerfile               # Instructions to build the Python backend image
├── docker-compose.yml       # Composes both NGINX and Python Backend services
└── nginx.conf               # Configuration for NGINX routing and WS proxy
```

## Your Mission

If you run `docker-compose up -d --build` right now, the containers will start, but the application will not work. You need to debug and fix the following three critical issues:

### 1. Fix the Docker Binding (Container Networking)
The FastAPI backend container is refusing external connections—even from the NGINX container! 
* **Hint:** Look at how the `uvicorn` command is binding its host in the `Dockerfile`. Inside a Docker container, binding to `localhost` or `127.0.0.1` makes the service unreachable to other containers on the Docker network.

### 2. Fix the Missing User Interface (Volume Mounts)
If you navigate to `http://localhost` right now, you will likely see the default "Welcome to NGINX" page instead of the chat application.
* **Hint:** Check `docker-compose.yml`. How is the `nginx` container supposed to get access to the static HTML files located in the local `frontend/` directory? 

### 3. Fix the WebSocket Tunnel (Reverse Proxy Configuration)
Once the UI is visible, the chat app will continuously say "Disconnected" because the WebSocket handshake is failing.
* **Hint #1:** In `nginx.conf`, the `proxy_pass` is attempting to route to `localhost:8000`. Does `localhost` mean the same thing inside the NGINX container as it does on your laptop? How do containers communicate with each other in a Compose network?
* **Hint #2:** NGINX requires explicit headers to convert standard HTTP traffic into a persistent WebSocket tunnel. Some of the required `Upgrade` headers appear to be missing or disabled.

## Deliverables

Submit your finalized, corrected codebase. We will evaluate your submission by executing:

```bash
docker-compose up -d --build
```

If everything is configured correctly, we should instantly see the UI and be able to open multiple browser tabs at `http://localhost` to chat back and forth in real-time. Good luck!
