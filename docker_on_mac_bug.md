# Docker on Mac Bug Documentation

## Environment
- macOS (as of April 2025)
- Docker Desktop for Mac

## Issue Description
When attempting to build Docker images on macOS, the build process fails with package download errors. This occurs with both Ubuntu 24.04 and Debian Stable base images, suggesting a broader issue with Docker on Mac's package handling.

## Reproduction Steps
1. Created Dockerfiles with Ubuntu 24.04 and Debian Stable as base images
2. Added standard package updates and installations (gcc, g++, Python, etc.)
3. Attempted to build using:
   ```bash
   docker build -t ubuntu-dev:24.04 ./docker/ubuntu2404
   docker build -t debian-dev:stable ./docker/debian_stable
   ```

## Errors Observed

### Ubuntu 24.04 Build Error
The build process failed when trying to use Ubuntu 24.04 as the base image.

### Debian Stable Build Error
The Debian Stable build also failed with multiple package hash mismatch errors:

```
E: Failed to fetch http://deb.debian.org/debian-security/pool/updates/main/p/perl/libperl5.36_5.36.0-7%2bdeb12u2_arm64.deb  Hash Sum mismatch
   Hashes of expected file:
    - SHA256:c894a8c2fcc5a2b900316727a74c9098915124c20e36b5b2a2427a86dd236990
   Hashes of received file:
    - SHA256:25534a600f94e84ae29c6060023d87b76f338956b6ce737baa66ae20f9589dce

E: Failed to fetch http://deb.debian.org/debian/pool/main/k/krb5/libk5crypto3_1.20.1-2%2bdeb12u2_arm64.deb  Hash Sum mismatch
...
```

The error shows multiple packages with hash sum mismatches, indicating that the packages being downloaded do not match what the package manager expects.

## Hypothesis
This issue appears to be related to:

1. Docker on Mac's container networking or caching mechanism interfering with package downloads
2. Package mirror issues when accessed through Docker's network stack on macOS
3. Potential architecture compatibility issues (the errors show arm64 packages)
4. Possible timestamp or date-related issues (noticed future dates in some error messages)

## Significance
The fact that both Ubuntu 24.04 and Debian Stable experience similar issues suggests this is not isolated to a specific Linux distribution, but rather points to a systemic issue with Docker on Mac's handling of package downloads or verification.

## Potential Workarounds
Possible workarounds to investigate:
1. Using different package mirrors
2. Implementing package caching strategies
3. Building with network=host option
4. Using multi-stage builds with pre-downloaded packages

## Successful Workaround
After testing on a Linux system (Ubuntu with kernel 6.11), we were able to successfully build the Debian Stable image using the `--load` flag:

```bash
docker build --load -t debian-dev:stable ./docker/debian_stable
```

### Explanation
The warning "No output specified with docker-container driver. Build result will only remain in the build cache..." occurs because Docker is using BuildKit with the docker-container driver by default. When using a standard `docker build` command, BuildKit builds the image in a container environment but doesn't automatically load it into Docker's local image repository.

Using `--load` explicitly tells Docker to load the built image into the local Docker daemon after building, making it available for immediate use without having to push it to a registry.

### Verification
The Debian Stable image builds successfully and includes all the required packages:

```
$ docker run -it --rm debian-dev:stable bash -c "cat /etc/os-release && echo && gcc --version"
PRETTY_NAME="Debian GNU/Linux 12 (bookworm)"
NAME="Debian GNU/Linux"
VERSION_ID="12"
VERSION="12 (bookworm)"
VERSION_CODENAME=bookworm
ID=debian
HOME_URL="https://www.debian.org/"
SUPPORT_URL="https://www.debian.org/support"
BUG_REPORT_URL="https://bugs.debian.org/"

gcc (Debian 12.2.0-14) 12.2.0
Copyright (C) 2022 Free Software Foundation Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

Note: While this successfully builds the Debian Stable image on a Linux system, this may or may not resolve the hash mismatch issues observed on macOS systems with Docker Desktop.
