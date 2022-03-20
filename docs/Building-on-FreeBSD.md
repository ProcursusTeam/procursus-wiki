Building on FreeBSD, much like Linux support, is made possible with
[cctools-port]. To setup Procursus on FreeBSD

1. Install dependencies

    You can install dependencies with `pkg`

        sudo pkg autoconf automake bash cmake coreutils docbook-xsl dpkg fakeroot findutils gettext git gmake gnugrep gnupg gsed gtar libtool ncurses openssl patch perl5 pkgconf po4a python39 wget zstd

    You'll also need to install `triehash`

        wget -O triehash https://raw.githubusercontent.com/julian-klode/triehash/main/triehash.pl
        gsed -i 's|#!/usr/bin/perl -w|#!/usr/bin/env perl -w|g' triehash
        sudo mv triehash /usr/local/bin

2. Setup your iOS toolchain

    [This script] will setup your SDKs, cctools-port with your iOS
    toolchain, and other dependencies needed.

    To run the script

        curl -LO "https://gist.githubusercontent.com/asdfugil/71cdfca5aa1bc0d59de06518cd1c530c/raw/37429856ec922063f86e60693bce4027d56aab96/procursus-utils-fbsd.sh" | bash

    It's recommended that you add this script to your shells' config
    file, so that it's sourced on every environment on login. Then,
    reopen your shell or reload your shell's configuration file.

        echo "source procursus-utils-fbsd.sh" > ~/.profile
        source ~/.profile

3. Clone the Procursus project

        git clone --recursive https://github.com/ProcursusTeam/Procursus.git; cd Procursus

4. Build!

    To check whether you did everything correctly, attempt to build
    `bash`. Checkout the [Build options] page to see what valid
    options can be passed

        make bash [OPTIONS]

    If the build is successful, congrats!. You should now be able to
    compile packages from Procursus.

    Similar to other supported platforms, it's likely that some
    packages (particularly those that need Python or NodeJS) will fail
    to compile. You'll need a macOS build system if this is the case.

[cctools-port]: https://github.com/tpoechtrager/cctools-port
[This script]: https://gist.github.com/asdfugil/71cdfca5aa1bc0d59de06518cd1c530c
[Build options]: https://github.com/ProcursusTeam/Procursus/wiki/Build-options
