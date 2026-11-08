# OpenTubeX Snap

This repository builds and publishes the official
[OpenTubeX](https://github.com/OpenTubeX/OpenTubeX) Snap package for `amd64`
and `arm64` systems.

## Install OpenTubeX

Install OpenTubeX from the [Snap Store](https://snapcraft.io/opentubex):

```sh
sudo snap install opentubex --beta
```

Snap keeps Store installations updated automatically. To opt in to development
snapshots, install from the edge channel instead:

```sh
sudo snap install opentubex --edge
```

Edge builds are intended for testing and may be unstable.

### Manual installation

You can also download the package for your architecture from
[GitHub Releases](https://github.com/OpenTubeX/snap/releases) and install it directly:

```sh
sudo snap install --dangerous ./opentubex_*.snap
```

Packages installed this way are not connected to the Snap Store and must be
updated manually.

## How publishing works

After an OpenTubeX stable or nightly release finishes uploading its packages,
the application repository sends a repository dispatch containing the exact
release tag. Nightlies are published to `edge`; regular releases are published to both
`beta` and `edge`. The publish workflow then:

1. downloads the release's `amd64` and `arm64` Debian packages and sets the
   Snap version;
2. builds both architectures using strict confinement and the Core 24 base;
3. uploads both packages to the selected Snap Store channels once both builds
   succeed. If the Snap Store holds a revision for manual review, it is not
   released and the workflow fails;
4. attaches regular-release packages to a GitHub release.

The workflow can also be run manually with a release tag. Manual runs attach
regular-release packages to a GitHub release and keep both builds as workflow
artifacts. Enable the `publish` input to also publish to the Snap Store; regular
releases use the selected channel and nightlies use `edge`.

Rerunning a tag with an immutable GitHub release keeps its published packages
after checking that both architecture assets are complete. Rebuilt packages can
still be uploaded to the Snap Store. To replace the downloadable GitHub packages,
publish a new release tag.

## Maintainer setup

1. Log in with `snapcraft login` using an account with publishing access to
   the registered `opentubex` Snap.
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
   test the downloaded package when changing the packaging.

The OpenTubeX application repository uses its existing `PUSH_TOKEN` secret to
send the cross-repository dispatch. That token needs write access to this
repository.
