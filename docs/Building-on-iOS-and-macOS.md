Building Procursus packages on iOS and macOS is fairly simple, since
these are the main target platforms. Make sure you have a decent amount
of free space on your device before starting.

This guide assumes that your device is already setup with a suit of
Procursus.

1. Install dependencies

    On macOS, you'll need the latest stable Xcode build. On iOS,
    install the following packages with your preferred package manager
    (e.g Sileo) or with APT

        sudo apt install clang dsymutil odcctools text-cmds

    Next, install the following packages with your preferred package
    manager or APT

        sudo apt install autoconf automake autopoint bash bison cmake curl docbook-xml docbook-xsl fakeroot findutils flex gawk git gnupg groff gzip ldid libtool make ncurses-bin openssl patch pkg-config po4a python3 sed tar triehash xz-utils zstd

2. Download your SDKs

    *If you're on macOS, you can skip this step.*

    To build Procursus packages, you'll need need a macOS SDK (your
    installed toolchain already contains an iOS SDK). Check the
    repositories below and get what you need

    - [phracker/MacOSX-SDKs] for macOS SDKs
    - [xybp888/iOS-SDKs] for iOS
    SDKs (in case you'd like to use a different SDK)

    You'd likely want to checkout the default values of your specific
    platform, since each support platform use a different path for SDKs

    Ideally, you can also export SDK variables from the [Build options]
    page on your shell configuration file

3. Clone the Procursus build system

        git clone --recursive https://github.com/ProcursusTeam/Procursus.git

4. Build!

    Building packages on iOS can be a hit or miss. However, macOS is
    the main platform where all packages should (and will) build

    To test your own build system setup, attempt to build `bash`.
    Checkout the [Build options] page to see what valid options can be
    passed

        make bash-package [OPTIONS]

[phracker/MacOSX-SDKs]: https://github.com/phracker/MacOSX-SDKs
[xybp888/iOS-SDKs]: https://github.com/xybp888/iOS-SDKs
[Build options]: https://github.com/ProcursusTeam/Procursus/wiki/Build-options
