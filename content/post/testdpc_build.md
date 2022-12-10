---
title: Build TestDPC with bazel 5.3
summary: Some personal notes on how to build in 2022 TestDPC with Bazel 5.2
date: 2022-11-20
author: Pietro F. Maggi
toc: true
hasMath: false 
categories:
    - "Android Enterprise"
    - "TestDPC"
    - "Bazel"
---

## The why

[TestDPC][0] is a fantastic tool for experimenting with Android enterprise's features. lately I wanted to play a bit with the [security logs][1] and TestDPC comes handy for this, but with a caviat: **the version included on the play store doesn't include some of the latest features**.

One of the feature not included are the cli commands to enable and retrieve security logs:

- `set-security-logging-enabled`
- `is-security-logging-enabled`
- `get-last-security-log-retrieval-time`
- `retrieve-security-logs`

these commands should be available using the syntax:

```bash
$adb shell dumpsys activity service com.afwsamples.testdpc is-security-logging-enabled
```

but these commands are not (yet) in the latest TestDPC release on the playstore (8.0.1 at this time).

If you want to see the complete list of the available commands in the version you are using, you can use the following command:

```bash
$adb shell dumpsys activity service com.afwsamples.testdpc help
```

The easy solution to this problem is to recompile the project. This means using bazel and the included project's configuration. But there is a problem:  
> If you're using `brew` on macOS, probably you are using `bazel` v5.3  
> **THIS VERSION DOESN'T WORK OUT OF THE BOX**.

## The how

Bazel v5.3 still uses `dx` for building TestDPC but this is no more in the latest Android's build tools, replaced by R8/D8. The easy solution? instruct Bazel to use an older build tools modifying the `WORKSPACE` file as:

```diff
    android_sdk_repository(
        name = "androidsdk",
        api_level = 33,
+       build_tools_version = "30.0.3",
    )
```

This is a step in the right direction, but not enough. We have to pass some additional information to bazel to built TestDPC correctly. This is my update `build.sh` file:

```bash
#!/bin/bash

# Fail on any error.
set -e

# Workaround to use d8 dexmerger - required before Bazel v6
# bazel build --noincremental_dexing testdpc
bazel build testdpc --define=android_dexmerger_tool=d8_dexmerger --define=android_incremental_dexing_tool=d8_dexbuilder --nouse_workers_with_dexbuilder --define=android_standalone_dexing_tool=d8_compat_dx --experimental_use_dex_splitter_for_incremental_dexing --incremental_dexing=true
````

Both changes are available on my personal [TestDPC branch][2]. With this you should be able to build TestDPC with the latest features released on github.

## Conclusion

Running `./build.sh` with the Android SDK setup correctly and having buildtools 30.0.3 installed, should result with the debug build of TestDPC available at: `bazel-bin/testdpc.apk`

If you want to try this latest TestDPC version, but you don't want to recompile it, you can use [my debug release available on github][3].

Let me know if you have any comment writing a message at pfm AT my personal domain or pinging me on mastodon.

[0]: https://github.com/googlesamples/android-testdpc.git
[1]: https://developer.android.com/work/dpc/security#log_enterprise_device_activity
[2]: https://github.com/pfmaggi/android-testdpc/tree/pfm/updates
[3]: https://github.com/pfmaggi/android-testdpc/releases/tag/20221116
