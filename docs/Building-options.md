This specific page documents specific build options and flags that can
be used in order to build packages for Procursus. The variables listed
here can all be found within the `Makefile`.

The table below showcases supported commands (along its function)
provided by Procursus

| Command | Function |
|---------|----------|
| `make`, `make all`, `make package` | Compiles the entire Procursus suit and packs it into Debian packages |
| `make env` | Prints the `proenv` shell function to `stdout` to give a cross-compilation environment in your POSIX shell (e.g `make env >> ~/.zshrc`) |
| `make everything` | Similar to `make` and `make all`, except that every supported host platform gets compiled and packed into Debian packages |
| `make (tool)` | Compiles a specific tool, sending headers and libraries to `build_base` so that they can be accessed by other packages |
| `make (tool)-package` | Similar to `make (tool)`. After compiling, the tool gets packed into a Debian package |
| `make rebuild-(tool)` | Re-builds an entire package |
| `make rebuild-(tool)-package` | Similar to `make rebuild-(tool)`. After re-compiling, the tool gets packed into a Debian package |
| `make (tool)-deps` | Finds and returns a list of dylibs linked to `(tool)` |
| `make clean` | Cleans out and removes `build_base`, `build_stage`, and `build_work` |
| `make extreme-clean` | Similar to `make clean`, cleaning out and removing `build_source`, `build_strap`, and `build_dist` |
| `make (tool)-download` | Downloads an already distributed package and puts it in `build_base`. This is specific to the actual name of the package |
| `make print-(variable)` | Prints out a variable from a Makefile. This can be used in order to obtain specific package information. |

These commands can have specific build flags attached to them; the
table below showcases build flags you can pass as options when using
Procursus build commands

| Variable | Required | Function | Example |
|----------|----------|----------|---------|
| `MEMO_TARGET` | Yes | Specifies architecture type that packages are built in | `iphoneos-arm64` |
| `MEMO_CFVER` | Yes | CoreFoundation value of the minimum *OS version to compile for | `1700`, which represents the CoreFoundation version that corresponds to iOS 14 |
| `DEB_MAINTANINER` | No | Sets the name and email of the package maintainer | `Procursus Team <support@procurs.us>`, by default |
| `NO_PGP` | No | Specifies whether GPG signature checks on tarballs should be skipped. Set this option to 1 to enable it | `NO_PGP=1` |
| `MEMO_QUIET` | No | Allows unnecessary warnings to be silenced. Set this option to 1 to enable it | `MEMO_QUIET=1` |
| `TARGET_SYSROOT` | Yes | Specifies the `PATH` of your iOS SDK. Check the `Makefile` to see what each supported build system defaults to | `/usr/share/SDKs/iPhoneOS.sdk` on iOS |
| `MACOSX_SYSROOT` | Yes | Specifies the `PATH` of your macOS SDK. Check the `Makefile` to see what each supported build system defaults to | `/usr/share/SDKs/MacOSX.sdk` on iOS |
| `BUILD_ROOT` | No | Allows for specific packages or architecture suits to sent to a different place once compiled, rather than within the Git repository itself | This can be any path, as long as the directory exists |
| `MEMO_FORCE_LTO` | No | Enables LTO. On macOS, LTO is enabled automatically, however on Linux and FreeBSD, it must be explicitly enabled in `cctools-port`. | `MEMO_FORCE_LTO=1` |
| `MEMO_ALT_LTO_LIB` | No | Specifies an alternative libLTO.{so,dylib} path. This will be passed to the linker with `-lto_library`. | `/usr/local/llvm11/lib/libLTO.so` |
| `MEMO_LDID_EXTRA_FLAGS` | No | Adds further arguments/flags for `ldid` | You can use this to specify a .p12 certificate with the -K flag. |
| `MEMO_CODESIGN_EXTRA_FLAGS` | No | Adds further arguments/flags for `codesign`, though it should only be used when signing, not when removing signatures. | |
| `MEMO_NO_IOSEXEC` | No | Disables linking `libiosexec` in every package hosted by Procursus.| `MEMO_NO_IOSEXEC=1` |
| `MEMO_NO_DOC_COMPRESS` | No | Disables compressing manpages for a specific package | `MEMO_NO_DOC_COMPRESS=1` |
| `MEMO_MANPAGE_COMPRESSION` | No | Specifies the type of compression used for manpages. Available options include `zstd`, `xz`, `gzip`, `lz4`, and `lzop`. Set to `none` to disable compression. | `MEMO_MANPAGE_COMPRESSION=zstd` |
