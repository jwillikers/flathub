# Libation Flatpak

The instructions here describe how to build and update the Libation Flatpak.

## Build

Get the source code.

    git clone https://github.com/flathub/com.getlibation.Libation.git
    cd com.getlibation.Libation

Add the Flathub repository.

    flatpak remote-add --user --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo

Install Flatpak Builder.

    sudo apt install flatpak-builder

Build the Flatpak.

    flatpak-builder --user --install --install-deps-from=flathub --force-clean --repo=repo build-dir com.getlibation.Libation.yaml

Run the Flatpak.

    flatpak run com.getlibation.Libation

## Update

A list of package source archives and checksums are generated for *all* of the Nuget dependencies.
Updating the Flatpak requires updating the Nuget source manifests.

You'll want to use the same version of nodejs as the node runtime for the Flatpak.

    sudo apt install pipx

Install yq.

    pipx install yq

Ensure the Flatpak Node Generator and yq are in your `PATH`.

    pipx ensurepath

Fetch the Libation source code.

    git clone https://github.com/rmcrackan/Libation.git

Checkout the appropriate tag.

    git -C Libation switch -d $(yq -r '.modules[-1].sources[-1].commit' com.getlibation.Libation.yaml)

Download the `flatpak-dotnet-generator.py` script.

    wget https://raw.githubusercontent.com/flatpak/flatpak-builder-tools/master/dotnet/flatpak-dotnet-generator.py

Install the necessary Flatpaks.

    flatpak --user install flathub org.freedesktop.Sdk//24.08 org.freedesktop.Sdk.Extension.dotnet8//24.08

Generate the Nuget sources manifest for `x86_64`.

    python flatpak-dotnet-generator.py --runtime linux-x64 --freedesktop 24.08 --dotnet 8 nuget-sources-x86_64.json Libation/Source/LibationAvalonia/LibationAvalonia.csproj

Generate the Nuget sources manifest for `aarch64`.

    python flatpak-dotnet-generator.py --runtime linux-arm64 --freedesktop 24.08 --dotnet 8 nuget-sources-aarch64.json Libation/Source/LibationAvalonia/LibationAvalonia.csproj

In the Flatpak manifest, be sure to update the .NET runtime as necessary.
