#shadow
your ai clone
# language: YAML, file: .github/workflows/build.yml
name: Build Shadow APK

on:
  push:
    branches: [ main ]
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 17

      - name: Setup Android SDK
        uses: android-actions/setup-android@v3

      - name: Download Vosk RU model
        run: |
          mkdir -p app/src/main/assets/model-ru
          wget -q https://alphacephei.com/vosk/models/vosk-model-small-ru-0.22.zip
          unzip -q vosk-model-small-ru-0.22.zip
          mv vosk-model-small-ru-0.22/* app/src/main/assets/model-ru/

      - name: Grant gradlew
        run: chmod +x gradlew

      - name: Build debug APK
        run: ./gradlew assembleDebug --no-daemon

      - name: Upload APK
        uses: actions/upload-artifact@v4
        with:
          name: shadow-apk
          path: app/build/outputs/apk/debug/app-debug.apk
