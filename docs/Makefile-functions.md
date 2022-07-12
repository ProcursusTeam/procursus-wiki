This specific page documents functions used in all Procursus packages,
each having its purpose. These functions can be found within the
`Makefile`.

The table below showcases current functions used across all Procursus
projects, and where to use them when creating a new package.

| Function | Description | Used in |
|----------|-------------|---------|
| [`SIGN`] | Recursively signs Mach-O libraries and binaries with `ldid` or `codesign` | Package stage (`(tool)-package`) |
| [`PACK`] | Creates a Debian package with `dpkg` or `dm.pl` for the given project | Package stage (`(tool)-package`) |
| [`DO_PATCH`] | Applies patches/changes necessary by a project to function | Setup stage (`(tool)-setup`) |
| [`AFTER_BUILD`] | Creates build system related files which indicate the status of a package (`.build_complete`) | Package function (`(tool)`), which inherits the package stage |
| [`EXTRACT_TAR`] | Extracts a tarball from `build_source` to `build_work` | Setup stage (`(tool)-setup`) |
| [`GITHUB_ARCHIVE`] | Downloads a Github archive from given parameters. This function makes it easier to download project files from Github | Setup stage (`(tool)-setup`) |
| [`GIT_CLONE`] | Much like `GITHUB_ARCHIVE`, but clones the specified repo using `git`. | Setup stage (`(tool)-setup`) |
| [`CHECKSUM_VERIFY`] | Checks the checksum of the package's tarball using the specified algorithm | Setup stage (`(tool)-setup`) |
| [`DOWNLOAD_FILE`] | Downloads a file to a direct path from the given URL | Setup stage (`(tool)-setup`) |
| [`DOWNLOAD_FILES`] | Like `DOWNLOAD_FILE`, but can take multiple URLs | Setup stage (`(tool)-setup`) |

## `SIGN`

This function recursively signs Mach-O libraries and binaries with
`ldid` or `codesign`. The table below showcase the required parameters
of the function

| Index | Description |
|-------|-------------|
| 1 | Name of the project with binaries; always in `build_dist` |
| 2 | File name that includes specific entitlements for binaries |

The second argument can be any string, as long as the entitlements are
within `build_misc/entitlements`.

#### Example

    $(call SIGN,nano,general.xml)

The example above looks for binaries within `build_dist/nano` and signs
them with entitlements specified in `build_misc/entitlements/general
xml`, which is often used to apply general entitlements to binaries and
Mach-O libraries.

## `PACK`

This functions creates a Debian package with `dpkg` or `dm.pl` for a
given project. The table below showcases all parameters required for
the function to work

| Index | Description |
|-------|-------------|
| 1 | Folder name where a control file might reside in for the project |
| 2 | Version variable that is in the control file that will be replaced |

The function looks for package files in `build_dist`, and puts them
accordingly, while also changing the package's control file, where
specific variables are replaced, such as the maintainer of the package,
and the version number

#### Example

    YOUTUBE_DL_VERSION  := 2021.06.06
    DEB_YOUTUBE_DL_V    ?= $(YOUTUBE_DL_VERSION)

    $(call PACK,youtube-dl,DEB_YOUTUBE_DL_V)

The example above packs files relating to `youtube-dl` (which would be
in `build_dist/youtube-dl`), and replaces `DEB_YOUTUBE_DL_V` in the
control file with the version number assigned to that variable.

## `DO_PATCH`

This function applies patches/changes necessary for a project to
function on a specific platform. The table below showcases all
parameters required for the function to work

| Index | Description |
|-------|-------------|
| 1 | Folder name where patches are located; generally inside `build_patch` |
| 2 | Folder name where patches should be applied to. This generally matches the name of the first argument, and the folder must exist in `build_work` |

*The last argument of the function is undocumented as it's generally
the same across all projects.*

#### Example

    $(call DO_PATCH,youtube-dl,youtube-dl,-p1)

The example above looks for patches inside `build_patch/youtube-dl` and
applies each patch (or diff) found to the project, `youtube-dl` (which
can be found in `build_work/youtube-dl`).

Patches are generally a good idea when dealing with a big project that
requires specific changes (that can't be put on the project's
repository or isn't part of the project's upstream yet) for a specific
platform.

## `AFTER_BUILD`

This function has several jobs. By default, the function will create
build system related files indicating that a package has been
successfully built. It will also compress included manpages for the
package being built.

The table below showcases optional arguments that the function can
take

| Index | Description |
|-------|-------------|
| 1 | Indicates if package files should be put within `build_base` |
| 2 | Folder indicating which packages should be marked as built | |

The function takes an optional argument, which indicates that package
files, once built, should be put withing `build_base`. This is useful
when other packages requires files from the package being built.

### Basic example

    lf: lf-setup
        ... # Do stuff here
        $(call AFTER_BUILD)

#### Copying files to `build_base`

    apt: apt-setup ... # Other dependencies
        ...
        $(call AFTER_BUILD,copy)

#### Marking other packages as finished

    byacc: byacc-setup
        ...
        # Only use this if other folders relate to a package
        $(call AFTER_BUILD,,byacc/byacc)
        $(call AFTER_BUILD,,byacc/byacc2)

## `GITHUB_ARCHIVE`

This function is used to download a Github archive of a specific
project. The following table showcases documentation for parameters
used by this function

| Index | Status | Description |
|-------|--------|-------------|
| 1 | Required | Github user or organization from which the project comes from |
| 2 | Required | Project/repository name from which an archive will be made from. This is also used as the filename for the downloaded tarball unless parameter 5 is given |
| 3 | Required | Specific parameter which appends the given version number to the downloaded tarball filename |
| 4 | Required | Release tag, branch name, or git hash from which an archive will be made from |
| 5 | Not required | Specifies a different name for the downloaded tarball. Use this if the tarball name is different than the repository name specified in parameter 3 |

There many ways in which you can manipulate this specific function in
your Makefile. The examples below showcase most instances

#### Tag example

    APPUNINST_VERSION := 1.0.0

    $(call GITHUB_ARCHIVE,quiprr,appuninst,$(APPUNINST_VERSION),v$(APPUNINST_VERSION))

    # URL:     https://github.com/quiprr/appuninst/archive/v1.0.0.tar.gz
    # tarball: $(BUILD_SOURCE)/appuninst-1.0.0.tar.gz

#### Commit example

    ZBRFIRMWARE_COMMIT := e4b7cf07bb491ecdbf08519063d7a9fa16aefdb8

    $(call GITHUB_ARCHIVE,zbrateam,Firmware,$(ZBRFIRMWARE_COMMIT),$(ZBRFIRMWARE_COMMIT))

    # URL:     https://github.com/zbrateam/Firmware/archive/e4b7cf07bb491ecdbf08519063d7a9fa16aefdb8.tar.gz
    # tarball: $(BUILD_SOURCE)/Firmware-e4b7cf07bb491ecdbf08519063d7a9fa16aefdb8.tar.gz

#### Branch example

    $(call GITHUB_ARCHIVE,tihmstar,jssy,master,master)

    # URL:     https://github.com/tihmstar/jssy/archive/master.tar.gz
    # tarball: $(BUILD_SOURCE)/jssy-master.tar.gz

#### Different tarball name example

    GHOSTBIN_COMMIT := 0e0a3b72c3379e51bf03fe676af3a74a01239a47

    $(call GITHUB_ARCHIVE,DHowett,spectre,v$(GHOSTBIN_COMMIT),$(GHOSTBIN_COMMIT),ghostbin)

    # URL:     https://github.com/DHowett/spectre/archive/0e0a3b72c3379e51bf03fe676af3a74a01239a47.tar.gz
    # tarball: $(BUILD_SOURCE)/ghostbin-v0e0a3b72c3379e51bf03fe676af3a74a01239a47.tar.gz

## `GIT_CLONE`

Like [`GITHUB_ARCHIVE`], except this function extends support for
projects that are outside of Github or don't provide any release
tarballs. Below is more documentation about specific parameters

| Index | Status | Description |
|-------|--------|-------------|
| 1 | Required | Link of the project that should be cloned |
| 2 | Required | Branch that will be checked out upon cloning |
| 3 | Required | Name of the folder where the project should be cloned to |

The folder where the files are cloned will always in `build_work`,
since the function doesn't take direct paths as arguments.

#### Example

    AOM_VERSION := 3.1.0

    $(call GIT_CLONE,https://aomedia.googlesource.com/aom.git,v$(AOM_VERSION),aom)

## `EXTRACT_TAR`

This function is used to extract a tarball downloaded with [`DOWNLOAD_FILE`],
[`DOWNLOAD_FILES`], or [`GITHUB_ARCHIVE`]. The table below documents the 3
arguments that this function requires

| Index | Status | Description |
|-------|--------|-------------|
| 1 | Required | Name of the downloaded tarball |
| 2 | Required | Name of the folder extracted from downloaded tarball |
| 3 | Required | Folder name where tarball files should be copied to |

The function does not take direct paths; the first argument looks for
tarballs in `build_source`, extracts the contents in that same
directory, and then copies them over to `build_work`.

#### Example

    APPUNINST_VERSION := 1.0.0

    $(call GITHUB_ARCHIVE,quiprr,appuninst,$(APPUNINST_VERSION),v$(APPUNINST_VERSION))
    $(call EXTRACT_TAR,appuninst-$(APPUNINST_VERSION).tar.gz,appuninst-$(APPUNINST_VERSION),appuninst)

The example above extracts the tarball downloaded from Github into
`build_source/appuninst-$(APPUNINST_VERSION)` — then, those files are
copied to `build_work/appuninst`.

## `CHECKSUM_VERIFY`

This function checks the hash of a package's tarball, downloaded through
[`DOWNLOAD_FILE`], [`DOWNLOAD_FILES`], or [`GITHUB_ARCHIVE`]. The table
contains further information on the specific arguments that this function
can receive

| Index | Status | Description |
|-------|--------|-------------|
| 1 | Required | Type of hash algorithm that should be used |
| 2 | Required | File name of the tarball whose checksum should be checked |

The first argument can be one out of the following algorithm: `sha1`, `sha256`, or `sha512`

### Example

    zstd-setup: setup
        ...
        $(call CHECKSUM_VERIFY,sha256,zstd-$(ZSTD_VERSION).tar.gz)
        ...

## `DOWNLOAD_FILE`

This function downloads a file from the given URL using `curl` to a
give direct path. This replace any direct calls to `wget`, and is the
new alternative to downloading project tarballs that GITHUB_ARCHIVE
cannot acquire.

The following table contains information about the arguments that the
function takes

| Index | Status | Description |
|-------|--------|-------------|
| 1 | Required | Direct path where the file will be downloaded to |
| 2 | Required | Single URL where the file is hosted |

### Example

    jlutil-setup: setup
        $(call DOWNLOAD_FILE,$(BUILD_SOURCE)/jlutil-$(JLUTIL_VERSION).tar.gz, \
                http://newosxbook.com/tools/jlutil.tgz)
        ...

## `DOWNLOAD_FILES`

Unlike [`DOWNLOAD_FILE`], this function downloads several files from a URL
(or list of URLs) to a specified directory path, and can be used to download
several tarballs needed for a project to fully build.

The following table contains information about the arguments that the
function takes

| Index | Status | Description |
|-------|--------|-------------|
| 1 | Required | Directory path where files will download to |
| 2 | Required | Single or list of URLs of files to download |

In some instances, developers provide extra files in project tarballs
through Github releases; Always use [`GITHUB_ARCHIVE`] unless this scenario
fits a specific project you're working on.

### Example

#### Downloading one file

    apt-setup: setup
        $(call DOWNLOAD_FILES,$(BUILD_SOURCE),https://salsa.debian.org/apt-team/apt/-/archive/$(APT_VERSION)/apt-$(APT_VERSION).tar.gz)
        ...

#### Downloading multiple files

<!-- Maybe rewrite this part later -->
*This function (due to how functions in Make work) has a small quirk.*

If you'd like to download multiple files, such as a public PGP key to check
the validity of a tarball with [`PGP_VERIFY`], make sure to use the variable,
`$(comma)` to separate URLs.

    # Before
    zstd-setup: setup
        wget -q -nc -P $(BUILD_SOURCE) https://github.com/facebook/zstd/releases/download/v$(ZSTD_VERSION)/zstd-$(ZSTD_VERSION).tar.gz{,.sig,.sha256}

    # with DOWNLOAD_FILES
    zstd-setup: setup
        $(call DOWNLOAD_FILES,$(BUILD_SOURCE),https://github.com/facebook/zstd/releases/download/v$(ZSTD_VERSION)/zstd-$(ZSTD_VERSION).tar.gz{$(comma).sig$(comma).sha256})


[`SIGN`]: #sign
[`PACK`]: #pack
[`DO_PATCH`]: #do_patch
[`AFTER_BUILD`]: #after_build
[`EXTRACT_TAR`]: #extract_tar
[`GITHUB_ARCHIVE`]: #github_archive
[`GIT_CLONE`]: #git_clone
[`CHECKSUM_VERIFY`]: #checksum_verify
[`DOWNLOAD_FILE`]: #download_file
[`DOWNLOAD_FILES`]: #download_files
