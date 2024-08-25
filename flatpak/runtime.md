# Create Custom Runtime

## Create the build directory
build-dir
 |-- runtime
 |  |-- files
 |  |-- manifest
 |-- sdk
 |  |-- files
 |  |-- manifest

### Root File System

In the **files** directory: put the root file system of the runtime.

The **sdl** file system should contain build tools

The **runtime** file system should contain the dependencies to run.

### Manifest

The manifest file must be filled as followed:

```
[Runtime]
name = net.domain.BasePlatform
runtime = net.domain.BasePlatform/x86_64/1.0
sdk = net.domain.BaseSdk/x86_64/1.0
```
Here x86\_64 would be te target architecture and 1.0 the version of the runtime

For the Sdk:

```
[Runtime]
name = net.domain.BaseSdk
runtime = net.domain.BasePlatform/x86_64/1.0
sdk = net.domain.BaseSdk/x86_64/1.0


## Build the flatpak repository

Flatpak runtimes are built using ostree. The script below will create a flatpak repository containing the runtime and associated sdk.

```
ostree init --mode archive-z2 --repo=$REPO_PATH
ostree commit -b runtime/net.domaine.BasePlatform/x86_64/2016-06-26 --tree=dir=runtime --repo=$REPO_PATH
ostree commit -b runtime/net.domaine.BaseSdk/x86_64/2016-06-26 --tree=dir=sdk --repo=$REPO_PATH
ostree summary -u --repo=$REPO_PATH
```

## Install the runtime

```
flatpak remote-add --no-gpg-verify local file://$REPO_PATH
flatpak install local net.domain.BasePlatform/1.0
```
