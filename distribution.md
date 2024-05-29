# Building and distributing MekaDB
This is as important as implementing MekaDB itself. It's a big effort upfront but is generally low maintenance once complete.
Most importantly, it adds incredible value for users to be able to install and use MekaDB the way they want.

There are a number of things that impact the build process needed:

Notes:

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
