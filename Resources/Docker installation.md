## Installing Docker on Windows
### 1. **Download Docker Desktop**
Visit the [Docker website](https://www.docker.com/products/docker-desktop/) and download the installer.

### 2. **Install Docker Desktop**
- Run the installer.
- Follow the prompts to complete the installation.

### 3. **Enable WSL2 Backend**
Ensure that Windows Subsystem for Linux 2 (WSL2) is enabled:
- Install WSL2 by following Microsoft's documentation.
- Enable WSL2 in Docker Desktop settings.

## Installing Docker on macOS
### 1. **Download Docker Desktop**
Visit the [Docker Desktop website](https://www.docker.com/products/docker-desktop/) and download the installer.

### 2. **Install Docker Desktop**
- Open the downloaded `.dmg` file.
- Drag Docker to the Applications folder.
- Launch Docker Desktop.

## Verify Installation
Open a terminal and run:
```bash
docker --version
```

## Post-Installation Steps
### Manage Docker as a Non-Root User
Add your user to the `docker` group:
```bash
sudo usermod -aG docker $USER
```
Log out and back in to apply the change.

### Test Docker Installation
Run a test container to ensure Docker is working:
```bash
docker run hello-world
```

