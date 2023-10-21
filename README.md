# EmergeTools-Perf-Analysis-Reproducer

Reproducer project for Emerge Tools Performance Analysis incompatibility.

## Project Configuration

| Component             | Version                                 |
|-----------------------|-----------------------------------------|
| Android Gradle Plugin | `8.1.1`                                 |
| Kotlin                | `1.8.21`                                |
| Android Studio        | `Android Studio Hedgehog 2023.1.1 RC 1` |

* Not necessarily need to match, but just in case any incompatibilities are encountered.

## Bug Description

Performance analysis tasks are not registering to the application project.

### Precondition

Set the following property to `true`

`gradle.properties`

```properties
org.gradle.configureondemand=true
```

See: [Configuration on demand](https://docs.gradle.org/current/userguide/multi_project_configuration_and_execution.html#sec:configuration_on_demand)

### Reproducing Steps

#### Configuration

`:app/build.gradle.kts`

```kotlin
plugins {
    id("com.emergetools.android") version "2.1.1"
}

emerge {
    performance {
        // Note that this is the relative path from the rootProject
        projectPath.set(":performance")
    }
}
```

#### Execution

Execute:

```terminal
$ ./gradlew :app:emergeGeneratePerformanceProject --package <package name>
```

Sync project.

Execute:

```terminal
$ ./gradlew :app:tasks
```

### Expected Behavior

Expected registered tasks:

```terminal
Emerge tasks
------------
emergeLocalDebugTest - Installs and runs tests for debug on connected devices. For testing and debugging.
emergeLocalReleaseTest - Installs and runs tests for release on connected devices. For testing and debugging.
emergeLocalSnapshotsDebug - Generate snapshots locally for variant Debug. Requires a device or emulator connected.
emergeUploadDebugAab - Builds and uploads an AAB for variant debug to Emerge.
emergeUploadDebugApk - Builds and uploads an APK for variant debug to Emerge.
emergeUploadDebugPerfBundle - Builds & uploads an AAB for variant debug to Emerge with performance test APK.
emergeUploadReleaseAab - Builds and uploads an AAB for variant release to Emerge.
emergeUploadReleaseApk - Builds and uploads an APK for variant release to Emerge.
emergeUploadReleasePerfBundle - Builds & uploads an AAB for variant release to Emerge with performance test APK.
emergeUploadSnapshotBundleDebug - Builds and uploads a snapshot bundle to Emerge. Snapshots will be generated on Emerge's cloud infrastructure and diffed based on the vcs params set in the Emerge plugin extension.
```

### Actual Behavior

Actual registered tasks:

```terminal
Emerge tasks
------------
emergeLocalSnapshotsDebug - Generate snapshots locally for variant Debug. Requires a device or emulator connected.
emergeUploadDebugAab - Builds and uploads an AAB for variant debug to Emerge.
emergeUploadDebugApk - Builds and uploads an APK for variant debug to Emerge.
emergeUploadReleaseAab - Builds and uploads an AAB for variant release to Emerge.
emergeUploadReleaseApk - Builds and uploads an APK for variant release to Emerge.
emergeUploadSnapshotBundleDebug - Builds and uploads a snapshot bundle to Emerge. Snapshots will be generated on Emerge's cloud infrastructure and diffed based on the vcs params set in the Emerge plugin extension.
```

## Additional Context

It appears the Emerge Tools Performance Analysis feature is incompatible
with `configuration on demand` gradle feature.
