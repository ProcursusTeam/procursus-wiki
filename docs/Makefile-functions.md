This specific page documents functions used in all Procursus packages, each having its purpose. These functions can be found within the `Makefile`.

The table below showcases current functions used across all Procursus projects, and where to use them when creating a new package.

| Function | Description | Used in |
|----------|-------------|---------|
| [`SIGN`](#sign) | Recursively signs Mach-O libraries and binaries with `ldid` or `codesign` | Package stage (`(tool)-package`) |
| [`PACK`](#pack) | Creates a Debian package with `dpkg` or `dm.pl` for the given project | Package stage (`(tool)-package`) |
| [`EXTRACT_TAR`](#extract_tar) | Extracts a tarball from `build_source` to `build_work` | Setup stage (`(tool)-setup`) |
| [`GITHUB_ARCHIVE`](#github_archive) | Downloads a Github archive from given parameters. This function makes it easier to download project files from Github | Setup stage (`(tool)-setup`) |
| [`GIT_CLONE`](#git_clone) | Much like `GITHUB_ARCHIVE`, but clones the specified repo using `git`. | Setup stage (`(tool)-setup`) |

## `SIGN`
This function recursively signs Mach-O libraries and binaries with `ldid` or `codesign`. The table below showcase the required parameters of the function

| Index | Description |
|-------|-------------|
| 1 | Name of the project with binaries; always in `build_dist` |
| 2 | File name that includes specific entitlements for binaries |

The second argument can be any string, as long as the entitlements are within `build_misc/entitlements`.

#### Example

    $(call SIGN,nano,general.xml)

The example above looks for binaries within `build_dist/nano` and signs them with entitlements specified in `build_misc/entitlements/general.xml`, which is often used to apply general entitlements to binaries and Mach-O libraries.

## `PACK`
This functions creates a Debian package with `dpkg` or `dm.pl` for a given project. The table below showcases all parameters required for the function to work

| Index | Description |
|-------|-------------|
| 1 | Folder name where a control file might reside in for the project |
| 2 | Version variable that is in the control file that will be replaced |

The function looks for package files in `build_dist`, and puts them accordingly, while also changing the package's control file, where specific variables are replaced, such as the maintainer of the package, and the version number

#### Example

    YOUTUBE_DL_VERSION := 2021.06.06
    DEB_YOUTUBE_DL_V ?= $(YOUTUBE_DL_VERSION)

    $(call PACK,youtube-dl,DEB_YOUTUBE_DL_V)

The example above packs files relating to `youtube-dl` (which would be in `build_dist/youtube-dl`), and replaces `DEB_YOUTUBE_DL_V` in the control file with the version number assigned to that variable.

## `GITHUB_ARCHIVE`
This function is used to download a Github archive of a specific project. The following table showcases documentation for parameters used by this function

| Index | Status | Description |
|-------|--------|-------------|
| 1 | Required | Github user or organization from which the project comes from |
| 2 | Required | Project/repository name from which an archive will be made from. This is also used as the filename for the downloaded tarball unless paramater 5 is given |
| 3 | Required | Specific paramter which appends the given version number to the downloaded tarball filename |
| 4 | Required | Release tag, branch name, or git hash from which an archive will be made from |
| 5 | Not required | Specifies a different name for the downloaded tarball. Use this if the tarball name is different than the repository name specified in parameter 3 |

There many ways in which you can manipulate this specific function in your Makefile. The examples below showcase most instances

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
Like `GITHUB_ARCHIVE`, except this function extends support for projects that are outside of Github or don't provide any release tarballs. Below is more documentation about specific parameters

| Index | Status | Description |
|-------|--------|-------------|
| 1 | Required | Link of the project that should be cloned |
| 2 | Required | Branch that will be checkout upon cloning |
| 3 | Required | Name of the folder where the project should be cloned to |

The folder where the files are cloned will always in `build_work`, since the function doesn't take direct paths as arguments.

#### Example

    AOM_VERSION := 3.1.0

    $(call GIT_CLONE,https://aomedia.googlesource.com/aom.git,v$(AOM_VERSION),aom)

## `EXTRACT_TAR`
This function is used to extract a tarball downloaded through `wget` or `GITHUB_ARCHIVE`. The table below documents the 3 arguments that this function requires

| Index | Status | Description |
|-------|--------|-------------|
| 1 | Required | Name of the downloaded tarball |
| 2 | Required | Name of the folder extracted from downloaded tarball |
| 3 | Required | Folder name where tarball files should be copied to |

The function does not take direct paths; the first argument looks for tarballs in `build_source`, extracts the contents in that same directory, and then copies them over to `build_work`.

#### Example

    APPUNINST_VERSION := 1.0.0

    $(call GITHUB_ARCHIVE,quiprr,appuninst,$(APPUNINST_VERSION),v$(APPUNINST_VERSION))
    $(call EXTRACT_TAR,appuninst-$(APPUNINST_VERSION).tar.gz,appuninst-$(APPUNINST_VERSION),appuninst)

The example above extracts the tarball downloaded from Github into `build_source/appuninst-$(APPUNINST_VERSION)` — then, those files are copied to `build_work/appuninst`.
