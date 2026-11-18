# libarchive [![Build](https://github.com/qnx-ports/build-files/actions/workflows/libarchive.yml/badge.svg)](https://github.com/qnx-ports/build-files/actions/workflows/libarchive.yml)

**NOTE**: QNX ports are only supported from a Linux host operating system

Crash analysis tools already exist in QNX like pidin stack, dumpcrash, or gdb to analyze crashes.These are more powerful and integrated with the OS. Additionally execinfo is not available on QNX. Thus disabled stacktrace.

Use `$(nproc)` instead of `4` after `JLEVEL=` if you want to use the maximum number of cores to build this project.

You can optionally set up a staging area folder (e.g. /tmp/staging) for <staging-install-folder> using `USE_INSTALL_ROOT=true` and INSTALL_ROOT_nto to <staging-install-folder> in below `make` command

# Compile the port for QNX in a Docker container

Pre-requisite: Install Docker on Ubuntu https://docs.docker.com/engine/install/ubuntu/

```bash
# Create a workspace
mkdir -p ~/qnx_workspace && cd ~/qnx_workspace
git clone https://github.com/qnx-ports/build-files.git

# Build the Docker image and create a container
cd build-files/docker
./docker-build-qnx-image.sh
./docker-create-container.sh

# Now you are in the Docker container

# Clone libarchive 
cd ~/qnx_workspace
git clone https://github.com/qnx-ports/libarchive.git

```

# Compile the port for QNX on Ubuntu host

```bash
# Clone the repos
mkdir -p ~/qnx_workspace && cd qnx_workspace
git clone https://github.com/qnx-ports/build-files.git
git clone https://github.com/qnx-ports/libarchive.git
# construct  configure  by running the script in build/autogen.sh
./build/autogen.sh

# source qnxsdp-env.sh
source ~/qnx710/qnxsdp-env.sh

# Build libarchive
make -C build-files/ports/libarchive install JLEVEL=4

#tested and verifid on vm710 x86

```

# Running tests

```bash
export TARGET_HOST=<target-ip-address-or-hostname>
# Copy shared libraries from QNX SDP
scp $QNX_TARGET/x86_64/usr/lib/libbz2.so.1 \
    $QNX_TARGET/x86_64/usr/lib/liblz4.so.1 \
    $QNX_TARGET/x86_64/usr/lib/liblzma.so.5 \
    $QNX_TARGET/x86_64/usr/lib/libxml2.so.16 \
    qnxuser@$TARGET_HOST:~/lib/

# Copy Libarchive test binary
scp $QNX_TARGET/x86_64/usr/share/test/libarchive/libarchive_test \
    qnxuser@$TARGET_HOST:~/bin/

# Copy .uu reference files
scp libarchive/libarchive/test/*.uu \
    qnxuser@$TARGET_HOST:~/bin/
    
```

```bash

### On target run

# Set shared library path
export LD_LIBRARY_PATH=$HOME/lib:$LD_LIBRARY_PATH

#  Navigate to test directory
cd ~/bin

#  Give executable permission
chmod +x libarchive_test

#  Run tests
./libarchive_test

```
