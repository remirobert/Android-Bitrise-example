# Android-Bitrise-example
Example Android application using the Bitrise CI.

Use the **[BETA] Virtual Device Testing for Android** workflow to run the UI tests.

## Bitrise.yml

```yml
---
format_version: '4'
default_step_lib_source: https://github.com/bitrise-io/bitrise-steplib.git
project_type: android
trigger_map:
- push_branch: "*"
  workflow: primary
- pull_request_source_branch: "*"
  workflow: primary
workflows:
  deploy:
    steps:
    - activate-ssh-key@3.1.1:
        run_if: '{{getenv "SSH_RSA_PRIVATE_KEY" | ne ""}}'
    - git-clone@3.6.2: {}
    - cache-pull@2.0.1: {}
    - script@1.1.5:
        title: Do anything with Script step
    - install-missing-android-tools@2.0.6: {}
    - gradle-runner@1.8.0:
        inputs:
        - gradle_file: "$GRADLE_BUILD_FILE_PATH"
        - gradle_task: assembleRelease
        - gradlew_path: "$GRADLEW_PATH"
    - deploy-to-bitrise-io@1.3.9: {}
    - cache-push@2.0.3: {}
  primary:
    steps:
    - activate-ssh-key@3.1.1:
        run_if: '{{getenv "SSH_RSA_PRIVATE_KEY" | ne ""}}'
    - git-clone@3.6.2: {}
    - install-missing-android-tools@2.0.6: {}
    - gradle-runner@1.8.0:
        inputs:
        - gradle_file: "$GRADLE_BUILD_FILE_PATH"
        - gradle_task: assembleDebug assembleDebugAndroidTest
        - gradlew_path: "$GRADLEW_PATH"
    - virtual-device-testing-for-android@0.9.9: {}
app:
  envs:
  - opts:
      is_expand: false
    GRADLE_BUILD_FILE_PATH: build.gradle
  - opts:
      is_expand: false
    GRADLEW_PATH: "./gradlew"
```
