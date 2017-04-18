# Testing with two emulators on CircleCI

Testing with emulators in CircleCI is really difficult because you can not use x86 emulators (which are way faster) and because of memory issues.

So in order to workaround that limitations, I have separated all my tests in phases so I do not use too much memory at the same time.

The `circle.yml` ended up like this:

```
test:
  pre:
    - ./gradlew clean --console=plain
    - mkdir app/build/coverage/
  override:
    - emulator -avd circleci-android24 -no-window:
        background: true
        parallel: true
    - circle-android wait-for-boot
    - ./gradlew createDebugCoverageReport --console=plain
    - cp app/build/outputs/code-coverage/connected/*coverage.ec app/build/coverage/
    - adb devices | grep emulator | cut -f1 | while read line; do adb -s $line emu kill; done
    - emulator -avd circleci-android22 -no-window:
        background: true
        parallel: true
    - circle-android wait-for-boot
    - ./gradlew createDebugCoverageReport --console=plain
    - cp app/build/outputs/code-coverage/connected/*coverage.ec app/build/coverage/
    - adb devices | grep emulator | cut -f1 | while read line; do adb -s $line emu kill; done
    - ./gradlew test --console=plain
```

Important details from this solution:
1. Copy the coverage reports to app/build/coverage because every time I run `createDebugCoverageReport` the output folder is cleaned. By copying to /app/build/coverage, they are available to be merged by JaCoCo later on test task, and they are also cleaned by `clean` task because they are inside the build folder.
1. Kill the emulator right after you end using it to free memory.
