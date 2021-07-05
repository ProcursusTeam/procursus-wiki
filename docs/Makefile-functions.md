This specific page documents functions used in all Procursus packages, each having its purpose. These functions can be found within the ``Makefile``.

The table below showcases current functions used across all Procursus projects, and where to use them when creating a new package.

| Function | Description | Use in |
|----------|-------------|---------|
| ``SIGN`` | Recursively signs Mach-O libraries and binaries with ``ldid`` or ``codesign``. | Package stage (``(tool)-package``) |
| ``PACK`` | Creates a Debian package with ``dpkg`` or ``dm.pl`` for the given project. | Package stage (``(tool)-package``) |
| ``EXTRACT_TAR`` | Extracts a tarball from ``BUILD_SOURCE`` to ``BUILD_WORK``. Use this to extract a downloaded tarball. | Setup stage (``(tool)-setup``) |
| ``GITHUB_ARCHIVE`` | Downloads a Github archive from given paramters. This function makes it easier to download project files from Github. | Setup stage (``(tool)-setup``) |
| ``GIT_CLONE`` | Much like ``GITHUB_ARCHIVE``, but clones the specified repo using ``git``. | Setup stage (``(tool)-setup``) |

## ``GITHUB_ARCHIVE``
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

## ``GIT_CLONE``
Much like ``GITHUB_ARCHIVE``, this function allows you to download a project more easily. However, this extends support for projects that are outside of Github. Below is more documentation about specific paramters.

| Index | Status | Description |
|-------|--------|-------------|
| 1 | Required | Link of the project that should be cloned |
| 2 | Required | Branch that will be checkout upon cloning |
| 3 | Required | Folder name that the project will be cloned to. This folder will always default to being inside ``BUILD_WORK`` |

#### Example

    AOM_VERSION := 3.1.0

    # Files are saved in BUILD_WORK/aom
    $(call GIT_CLONE,https://aomedia.googlesource.com/aom.git,v$(AOM_VERSION),aom) 

## ``EXTRACT_TAR``
This function is used in order to extract a tarball downloaded through ``wget`` or through the ``GITHUB_ARCHIVE`` function. The table below documents the 3 arguments that this function requires.

| Index | Status | Description |
|-------|--------|-------------|
| 1 | Required | Name of the downloaded tarball |
| 2 | Required | Folder name extracted by the tarball |
| 3 | Required | Path where tarball files should be copied to |

The function does not take direct paths; the first argument looks for tarballs in ``build_source``, extracts the contents in that same directory, and then copies them over to ``build_work``. The example below showcases a tarball of Github project being downloaded, then extracted

    APPUNINST_VERSION := 1.0.0

    # Under your package setup function
    $(call GITHUB_ARCHIVE,quiprr,appuninst,$(APPUNINST_VERSION),v$(APPUNINST_VERSION))
    $(call EXTRACT_TAR,appuninst-$(APPUNINST_VERSION).tar.gz,appuninst-$(APPUNINST_VERSION),appuninst)

The example above extracts the tarball downloaded from Github into ``build_source/appuninst-$(APPUNINST_VERSION)`` — then, those files are copied to ``build_work/appuninst``.
