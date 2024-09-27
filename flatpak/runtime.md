# Create Custom Runtime

## Create the runtime directory

It should have the following structure

runtime-folder
 |-- manifest
 |-- var
 |  |-- run -> /run
 |  |-- tmp
 |-- files
 |-- usr
 |  |--bin
 |  |--lib
 |  |--etc
 |  |.....


Where manifest is a file, and var, files and usr are root directories.
var/run is a symlink to machine /run
var/tmp is an empty folder
files will contain the application built on top of the runtime
usr contain the runtime, the libraries, the binaries and any other standard linux filesystem utils

## Manifest

The manifest file must be filled as followed:

```
[Runtime]
name = net.domain.BasePlatform
runtime = net.domain.BasePlatform/x86_64/1.0
sdk = net.domain.BaseSdk/x86_64/1.0
```
Here x86\_64 would be te target architecture and 1.0 the version of the runtime

## Push Runtime
Add the new runtime to the repository that you already created.
```
flatpak build-export /path/to/local/repo/  /path/to/runtime/folder/
```

