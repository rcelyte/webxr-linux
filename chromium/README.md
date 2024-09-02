# Chromium
This folder contains a patch for Chromium to compile with OpenXR support under Linux, loosely based on [this PR](https://github.com/chromium/chromium/pull/95).

# How to compile
In order to apply and compile this patch, it's important that you are able to compile Chromium yourself. For this follow the steps over at: https://chromium.googlesource.com/chromium/src/+/main/docs/linux/build_instructions.md

Once you have the Chromium code base locally, apply the patch using the the following command (or use `git apply`):

```
git am /path/to/webxr-linux/chromium/webxr-linux-vulkan.patch
```

> **Note:** These patches were made on top of Chromium 130.0.6692.0, commit `500a778620f8f8c1305d37f83897e8a0e8739851`, and hopefully apply cleanly to more recent commits as well.

Once applied, build using `autoninja`. In order for chromium to find your configured OpenXR runtime make sure to launch it with the `--no-sandbox` flag
```
./chrome --no-sandbox
```

## Other relevant things to mention
* ***Overlay compositing is not implemented yet.*** In-headset permission prompts will be invisible but still interactable.
* Attempting to start a new WebXR session after reloading or otherwise navigating away from all pages previously utilizing WebXR will result in the error `Error [GENERAL | xrCreateInstance | OpenXR-Loader] : Loader does not support simultaneous XrInstances`, preventing WebXR from functioning until the browser is restarted.
* A copy of [this patch](https://chromium-review.googlesource.com/c/chromium/src/+/4361799) is included to fix a texture sharing issue.
* `GLES2Interface::Finish()` is used as a (far less performant) substitute for `ContextSupport::GetGpuFence()` since Chromium doesn't implement support for GPU fences on non-Android Linux.
* As of September 2024, SteamVR's OpenXR implementation has a bug which deadlocks the process on instance destroy or exit, preventing subsequent WebXR sessions from starting after the first one finishes. See [this issue](https://github.com/ValveSoftware/SteamVR-for-Linux/issues/422) for more details.