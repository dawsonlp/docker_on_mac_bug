# Docker on Mac Bug Demonstration

This project demonstrates a potential bug with Docker on macOS using Ubuntu 24.04 as a base image, with a comparison to Debian Stable.

## Project Structure

```
docker_on_mac_bug/
├── docker/
│   ├── ubuntu2404/
│   │   └── Dockerfile
│   └── debian_stable/
│       └── Dockerfile
├── README.md
├── design_decisions.md
└── docker_on_mac_bug.md
```

## Requirements

- Docker Desktop for Mac
- macOS (latest version recommended)

## What This Docker Setup Does

The Dockerfiles in this project:
- Use either Ubuntu 24.04 or Debian Stable as base images
- Update and upgrade the system packages
- Install development tools:
  - gcc
  - g++
  - Python with development headers
  - pip
  - Python virtual environment support

## Building the Docker Images

To build the Docker images, run one of the following commands from the root of this repository:

```bash
# Build Ubuntu 24.04 image (may fail due to the documented bug)
docker build -t ubuntu-dev:24.04 ./docker/ubuntu2404

# Build Debian Stable image (as comparison)
docker build -t debian-dev:stable ./docker/debian_stable
```

## Running a Container

After building the image successfully, you can run a container with:

```bash
# For Ubuntu 24.04 (if build succeeds)
docker run -it --rm ubuntu-dev:24.04

# For Debian Stable
docker run -it --rm debian-dev:stable
```

This will give you a bash shell inside the container with all the development tools installed.

## Testing Specific Tools

Once inside the container, you can verify the installations:

```bash
gcc --version
g++ --version
python --version
pip --version
```

## Issues and Observations

See `docker_on_mac_bug.md` for documented observations regarding any issues encountered when building or running this Docker image on macOS.
