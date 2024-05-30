# Building and distributing MekaDB
This is as important as implementing MekaDB itself. It's a big effort upfront but is generally low maintenance once complete.
Most importantly, it adds incredible value for users to be able to install and use MekaDB the way they want.

Cross compiling is non-trivial however you need to:
* Run on the right host
* If you can't install the right dependencies and tell cargo which ones to point at
* Distinguish between 32/64 ARM/AMD CPUs 

This is doable with Github actions but takes fiddling.

# General
* Use `rustup target list` to see the list of targets Rust supports
* You can install a toolchain using `rustup toolchain install <version>-<target>` e.g. `rustup toolchain install stable-aarch64-apple-darwin`
* Use `rustup target add <target>` to add a target e.g. `aarch64-apple-darwin` - not all targets can be installed on a host
* It is generally easier to build on a machine that is native to the target though this isn't always possible it should be preferred when available
* Try to statically link if possible
  * Use `export RUSTFLAGS="-C target-feature=+crt-static"` to tell cargo to link the C runtime statically. It is apparently a `musl` only thing but I've not been able to get a musl build
    * Some potentially dependencies for this to work with musl - https://users.rust-lang.org/t/target-x86-64-unknown-linux-musl-fails-to-link/50401/5 
    * https://stackoverflow.com/a/44387312/400048
    * A suggest on how to set the linker for musl - https://users.rust-lang.org/t/how-to-install-armv7-unknown-linux-musleabihf/82395/2 
    * Some dependencies which may be needed to make musl work - https://users.rust-lang.org/t/how-to-use-rustflags-environment-variable-to-link-to-library/110108/6
* Start small - build the binaries independently of Docker, leverage Github releases
  * Automatically create a Github release on commit
  * Trigger a build of the various binaries after the release is created https://stackoverflow.com/a/64733705/400048
  * Make sure to check that the workflow below completed with a success status https://github.com/orgs/community/discussions/26238#discussioncomment-3250901
  * Official docs on it https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#workflow_run
* Available Github runner images are listed here https://github.com/actions/runner-images?tab=readme-ov-file#available-images
  * Docs https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners/about-github-hosted-runners#standard-github-hosted-runners-for-public-repositories
  * `macos-13` is Intel/X86
  * `macos-14` is arm64/M1
  * Github should support Intel mac for some time so we can continue building the easier way
  * There's no Github hosted Windows ARM runner but one could be self-hosted - https://github.blog/changelog/2022-09-28-github-actions-self-hosted-runners-now-support-windows-arm-hardware/
    * ARM is increasingly important on Desktop and server for all major OSs so we'll need to do a Windows ARM build at some point
  * 

# Linux ARM
* Ubuntu has a series of arm-* packages for GCC, these are NOT what are needed. They will build 32 bit binaries
* Use the gcc-aarch64* and g++-arch64* ubuntu packages
* Specify the linker that cargo should use using `CARGO_TARGET_AARCH64_UNKNOWN_LINUX_GNU_LINKER`
  * This is based on the machine triple so if we want to add a 32 bit ARM build we would change it to `CARGO_TARGET_ARM_UNKNOWN_LINUX_GNU_LINKER`

# Mac 
* It seems impossible to get Docker desktop to start on Mac
  * `brew install --cask docker` installs it but that's just the client https://stackoverflow.com/questions/40523307/brew-install-docker-does-not-include-docker-engine
  * `open -a Docker` doesn't start it either
  * https://stackoverflow.com/questions/40523307/brew-install-docker-does-not-include-docker-engine/43365425#43365425
  * https://stackoverflow.com/questions/54437744/how-to-start-docker-from-command-line-in-mac
  * https://stackoverflow.com/a/77932275/400048
  * Not yet tested but seems maybe signing the mac app again could allow it to start - https://stackoverflow.com/a/64985297/400048
    * Some explanation of that theory - https://developer.apple.com/forums/thread/684509?answerId=682326022#682326022
    * Diagnosing Docker issue - https://docs.docker.com/desktop/troubleshoot/overview/#diagnose-from-the-terminal
  * Request to add Docker to runners https://github.com/actions/runner-images/issues/2150
  * There's this Official experimental thing from Docker - https://github.com/docker/desktop-action BUT it uses ubuntu?
  * Had the native mac runner not worked I found osxcross
    * https://github.com/tpoechtrager/osxcross
    * and an action to setup in a pipeline https://github.com/marketplace/actions/setup-osxcross
    * action based on post here https://jamwaffles.github.io/rust/2019/02/17/rust-cross-compile-linux-to-macos.html/
    * 
# Windows x86

* We used tokio-rusttls which uses AWS's
  * It requires NASM and CMAKE - see https://aws.github.io/aws-lc-rs/requirements/windows.html
  * Installing manually was painful and didn't work but found `ilammy/setup-nasm@v1` action sets it up properly
  * See https://github.com/rustls/rustls/issues/1913 where it was discussed
* Windows Dockerfile docs https://learn.microsoft.com/en-us/virtualization/windowscontainers/manage-docker/manage-windows-dockerfile
* A MySQL Dockerfile example showing how to modify path with Powershell https://github.com/MicrosoftDocs/Virtualization-Documentation/blob/main/windows-container-samples/mysql/Dockerfile

# Github

* Github release
  * Create command docs - https://cli.github.com/manual/gh_release_create
  * Download command docs - https://cli.github.com/manual/gh_release_download
  * Edit command docs - https://cli.github.com/manual/gh_release_edit

# Docker
* Everyone seems to use QEMU for cross platform builds - https://github.com/docker/setup-qemu-action
  * Found many issues with it including builds that complete but don't run on the target afterwards
* Workflow builds
  * Old attempt for multiplatform docker https://github.com/hypi-universe/mekadbms/commit/ab5c46fa238d7483f9350465c1638911dfb998a4
  * Old attempt for windows https://github.com/hypi-universe/mekadbms/commit/4e00a5161547b0d1503a551f680f1069caa3e99e
* A clue about multi-arch builds https://github.com/docker/build-push-action/issues/671#issuecomment-1373055782
* This is the "modern" way to do it https://docs.docker.com/build/ci/github-actions/multi-platform/#distribute-build-across-multiple-runners but doesn't work on windows 
* I then found this https://medium.com/@life-is-short-so-enjoy-it/docker-how-to-build-and-push-multi-arch-docker-images-to-docker-hub-64dea4931df9 which explains
* Importantly, it lead to https://www.docker.com/blog/multi-arch-build-and-images-the-simple-way/ which says it is the "hard" way...but it turned out to be the good way for us
Links:

1. https://www.docker.com/blog/multi-arch-build-and-images-the-simple-way/
2. https://github.com/cross-rs/cross/issues/1241
3. Sample cross Github action setup https://github.com/dirien/rust-cross-compile
4. It is based on the action https://github.com/houseabsolute/actions-rust-cross
5. Where i learned about cargo target - https://users.rust-lang.org/t/building-executable-for-alpine-linux/13568/2
6. Attempted to use `rustup run <target> cargo run` but that lead to git dependency issues
7. In the end, manually symlinking from the arm directory to /usr/lib works when combined with `CARGO_TARGET_ARM_LINUX_GNUEABIHF_LINKER`. See the deploy.yaml action for ARM
8. See https://github.com/rust-lang/rust/issues/28924#issuecomment-580828030
9. Someone from Rust discord channel suggesting using `cargo zigbuild` - it apparently makes this easier https://github.com/rust-cross/cargo-zigbuild?tab=readme-ov-file
10. The also said the `xx` tool is a docker based approach that works well to
11. See https://github.com/tonistiigi/xx?tab=readme-ov-file#rust - it looks interesting but we'd need to manually install dependencies into it
12. So we'd need a multi-stage docker build, stage 1 would use `xx` and then stage two is the current Dockerfile. We'd abandon most of the Github action steps to do it this way
13. It's attractive but comes late in the game after Github is working, there may be a compelling enough reason in the future to revisit though, Github is looking to get complex
14. See the discord thread here https://discord.com/channels/273534239310479360/1244835121769484289
15. If we ever go the `rustup run` route again, the error that blocked my path was `Object not found - no match for id`
16. It would fail to use a dependency from a git repo and died with this error. Lead to `rm`ing some cargo dirs which is in the github action still but may not be needed anymore
17. It also lead to using vendored dependencies for ARM...possible to remove but no reason to as it works well. I came across issues linked to it
    1. https://github.com/rust-lang/cargo/issues/3561
    2. https://github.com/rust-lang/cargo/pull/5110
    3. https://github.com/rust-lang/cargo/issues/8705
18. None of them fixed the issue for me though.
19. Look at using other tools
    1. Conan - https://github.com/conan-io/conan/issues/15479
    2. https://github.com/Devolutions/conan-rs
20. For any arm builds with `Relocations in generic ELF` errors, see point 7. symlink + linker environment variable should resolve https://github.com/rust-lang/rust/issues/28924#issuecomment-580828030
21. Os specific packaging
    1. Mac `pkg` - https://github.com/ripeda/macOS-Pkg-Builder - this looks better than the one below
    2. Mac `pkg` - https://github.com/KosalaHerath/macos-installer-builder
    3. Linux `.deb` - https://github.com/marketplace/actions/build-deb-action
    4. Linux `.rpm` - https://github.com/marketplace/actions/build-rpm-action
    5. Windows MSI - https://github.com/marketplace/actions/build-windows-installer-msi-from-exe-file
