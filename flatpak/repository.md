# Hosting a Flatpak Repository

## Init a repository

```
flatpak build-init $REPOSITORY_PATH
```

## Run Flatpak Build

In this step, we build the flatpak from the yaml or json manifest and directly push it into the repository folder

```
flatpak --user --force-clean --repo=$REPOSITORY_PATH --install-deps-from=flathub $BUILD_PATH  $MANIFEST_YAML
```

## Build Flatpak Application

First step is to test the build process of the application
```
flatpak-builder --user --force-clean --install-deps-from=flathub build-dir manifest.yaml --ccache
```
Then you can push the flatpak built into your repository `flatpak build-export /path/to/local/repo build-dir`

It is also possible to build and push to local repo directly with
```
flatpak-builder --user --force-clean --repo=/path/to/local/repo --install-deps-from=flathub build-dir manifest.yaml --ccache
```

## Sign Repository

This step aims to sign the differents build using our GPG key

```
flatpak build-sign --gpg-sign=$KEY_ID $REPOSITORY_PATH
flatpak build-update-repo --gpg-sign=$KEY_ID $REPOSITORY_PATH
```

## Install Remote Repository on Client

To add a remote repository and the associated signature key
```
flatpak remote-add --gpg-import=my_gpg_public_key.gpg my-repo http://my.server.name/repo/local.flatpakrepo
```

If you host your repository locally on your machine, you can use a local path
```
flatpak remote-add --gpg-import=my_gpg_public_key.gpg my-repo file://path/to/local/repo/
```

Then you can install a flatpak from this repo using `flatpak install my-repo org.example.MyApp`

## Remove Flatpak from Repository

To list the apps and runtimes references: `ostree --repo=$REPOSITORY_PATH refs`

Then we can remove the flatpak using it reference : `ostree --repo=$REPOSITORY_PATH refs --delete $REFERENCE`
Then we clean the updates not used : `flatpak build-update-repo --prune $REPOSITORY_PATH`
