name: Build NovaLoader APK

on:
  workflow_dispatch:
  push:
    paths:
      - 'NovaLoaderAndroid_v1.1_Project.zip'
      - '.github/workflows/build.yml'

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Download project
        uses: actions/checkout@v4

      - name: Install Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'

      - name: Install Android SDK
        uses: android-actions/setup-android@v3

      - name: Install Gradle
        uses: gradle/actions/setup-gradle@v4
        with:
          gradle-version: '8.9'

      - name: Unpack project
        run: |
          mkdir project
          unzip -q NovaLoaderAndroid_v1.1_Project.zip -d project
          PROJECT_DIR=$(dirname "$(find project -name settings.gradle.kts -print -quit)")
          echo "PROJECT_DIR=$PROJECT_DIR" >> $GITHUB_ENV

      - name: Build APK
        run: gradle -p "$PROJECT_DIR" assembleDebug

      - name: Save APK
        uses: actions/upload-artifact@v4
        with:
          name: NovaLoader-APK
          path: project/**/app/build/outputs/apk/debug/*.apk
