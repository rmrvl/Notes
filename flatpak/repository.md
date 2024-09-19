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

## Sign Repository

This step aims to sign the differents build using our GPG key

```
flatpak buil-sign --gpg-sign=$KEY_ID $REPOSITORY_PATH
flatpak buil-update-repo --gpg-sign=$KEY_ID $REPOSITORY_PATH
```

## Install Remote Repository on Client

```
flatpak remote-add --gpg-import=my_gpg_public_key.gpg my-repo
```

Then you can install a flatpak from this repo using `flatpak install my-repo org.example.MyApp`

## Remove Flatpak from Repository

To list the apps and runtimes references: `ostree --repo=$REPOSITORY_PATH refs`

Then we can remove the flatpak using it reference : `ostree --repo=$REPOSITORY_PATH refs --delete $REFERENCE`
Then we clean the updates not used : `flatpak build-update-repo --prune $REPOSITORY_PATH`
