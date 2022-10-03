# Chrysalis Flatpak

The instructions here describe how to build and update the [Chrysalis](https://github.com/keyboardio/Chrysalis) Flatpak.

## Build

Get the source code.

    git clone --recursive https://github.com/flathub/io.keyboard.Chrysalis.git
    cd io.keyboard.Chrysalis

Add the Flathub repository.

    flatpak remote-add --user --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo

Install Flatpak Builder.

    sudo apt install flatpak-builder

Build the Flatpak.

    flatpak-builder --user --install --install-deps-from=flathub --force-clean --repo=repo build-dir io.keyboard.Chrysalis.yaml

Run the Flatpak.

    flatpak run io.keyboard.Chrysalis

## Update

A list of package source archives and checksums are generated for *all* dependencies.
Ideally, these dependencies will be updated whenever the `yarn.lock` file changes to keep the Flatpak version using the same packages.
The Flatpak Node Generator is used to generate this list of packages from the `yarn.lock` file, which is included directly in the Flatpak manifest.
To generate an updated version of this file, `generated-sources.json`, follow the instructions here.

Make sure to install `yarn`.

    sudo apt install jq pipx python3-aiohttp yarnpkg

Install yq.

    pipx install yq

Install the Flatpak Node Generator Python utility with `pipx`.

    pipx install flatpak-builder-tools/node

Ensure the Flatpak Node Generator and yq are in your `PATH`.

    pipx ensurepath

Fetch the Stretchly source code.

    git clone https://github.com/keyboardio/Chrysalis.git

Checkout the appropriate tag.

    git -C Chrysalis switch -d $(yq -r '.modules[4].sources[0].commit' io.keyboard.Chrysalis.yaml)

Run the script against the `yarn.lock` file as shown here.

    flatpak-node-generator yarn Chrysalis/yarn.lock --electron-node-headers --recursive
