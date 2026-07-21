# OpenTubeX Snap

This repository builds and publishes the official
[OpenTubeX](https://github.com/OpenTubeX/OpenTubeX) Snap package for `amd64`
and `arm64` systems.

## Install OpenTubeX

The package is initially published to the beta channel while OpenTubeX is in
beta:

```sh
sudo snap install opentubex --beta
```

## How publishing works

After an OpenTubeX release finishes uploading its packages, the application
repository sends an `opentubex-release` repository dispatch containing the
release tag. The publish workflow then:

1. validates that the release contains the required `amd64` and `arm64`
   Debian packages;
2. updates the Snap version for that workflow run;
3. builds both architectures using strict confinement and the Core 24 base;
4. uploads the resulting packages to the Snap Store beta channel.

The workflow can also be run manually with a release tag. Manual runs can
build downloadable artifacts without publishing them.

## Maintainer setup

1. Log in with `snapcraft login` and register the name with
   `snapcraft register opentubex`.
2. Export credentials restricted to this package:

   ```sh
   snapcraft export-login \
     --snaps=opentubex \
     --channels=stable,candidate,beta,edge \
     --acls=package_access,package_push,package_update,package_release \
     snapcraft-login.txt
   ```

3. Add the contents of that file as the `SNAPCRAFT_STORE_CREDENTIALS`
   repository secret.
4. Run the **Build and publish Snap** workflow with publishing disabled and
   test the downloaded package before the first Store release.

The OpenTubeX application repository uses its existing `PUSH_TOKEN` secret to
send the cross-repository dispatch. That token needs write access to this
repository.
