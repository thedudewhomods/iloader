# iLoader Android port status

This branch is a safe starting point for the Android port. It deliberately does **not** claim that Android USB sideloading is implemented yet.

## What this branch changes

- Adds a dedicated Android arm64 build workflow.
- Avoids the previous workflow's incorrect assumption that checking out the `easy` repository and adding a `rusbmux` Cargo dependency automatically connects iLoader to iOS devices.
- Keeps the Android build isolated from the existing desktop build workflow.

## Required implementation before this becomes a working sideloader

1. **Android USB Host transport:** implement a backend using Android's `UsbManager`, runtime USB permission requests, interface claiming, bulk IN/OUT transfers, and detach handling. The current `rusbmux` `nusb` backend cannot directly use an Android `UsbDeviceConnection`.
2. **rusbmux integration:** vendor or depend on the complete Easy/rusbmux library and connect its `RusbmuxProvider` to the Android USB backend. Copying `provider.rs` alone is insufficient because it depends on the device, packet router, connection, watcher, and backend modules.
3. **iLoader transport abstraction:** refactor `device.rs`, `pairing.rs`, and `sideload.rs` so discovery, pairing, app installation, and service connections all use the selected provider. Preserve the current usbmuxd path for desktop.
4. **Android storage:** replace or cfg-gate desktop-native keyring backends and use Android-compatible secure storage for credentials and pairing material.
5. **Android app integration:** add USB Host capability and device attach/permission handling to the generated Tauri Android project.
6. **Validation:** run the Android build, then test device discovery, Lockdown pairing, AFC/House Arrest, InstallationProxy, and an actual IPA installation on physical hardware.

## Important

A successful APK build only proves that the app compiles. It does not prove that USB discovery, pairing, or sideloading works. Do not label this port complete until the physical-device checks above pass.
