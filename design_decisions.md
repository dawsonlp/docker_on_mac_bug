# Design Decisions

## Project Structure
- Created a clear directory structure with separate directories for each distribution (`docker/ubuntu2404` and `docker/debian_stable`)
- This modular approach isolates each Docker environment and allows for easy comparison between different base images

## Dockerfile Decisions
- Used Ubuntu 24.04 and Debian Stable as base images for comparison
- Set `DEBIAN_FRONTEND=noninteractive` to avoid prompts during package installation
- Combined multiple `apt-get` commands with `&&` to reduce image layers
- Included cleanup steps (`apt-get clean` and `rm -rf /var/lib/apt/lists/*`) to reduce image size
- Added verification steps to confirm installed tools are working properly
- Set up `/app` as the working directory following container best practices
- Used a symlink to make Python 3 the default python command for convenience

## Best Practices Applied
- Used specific version tagging (24.04 for Ubuntu) and semantic tags (stable for Debian) rather than generic tags like "latest"
- Minimized the number of layers by combining commands
- Cleaned up package lists to reduce image size
- Used environment variables to control installation behavior
- Verified installations as part of the build process
- Provided clear documentation in README.md
- Added comparison testing with Debian Stable to validate whether the issue is specific to Ubuntu 24.04
- Created a comprehensive bug documentation file (docker_on_mac_bug.md) to record observations and findings
