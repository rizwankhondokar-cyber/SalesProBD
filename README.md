name: Build SalesProBD APK

on:
  workflow_dispatch:

permissions:
  contents: read

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '21'

      - name: Set up Android SDK
        uses: android-actions/setup-android@v3

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '22'

      - name: Extract app project
        run: |
          unzip SalesProBD_Android_Starter.zip -d appsrc

      - name: Install app dependencies
        working-directory: appsrc
        run: npm install

      - name: Add Android platform
        working-directory: appsrc
        run: npx cap add android

      - name: Sync Android project
        working-directory: appsrc
        run: npx cap sync android

      - name: Build APK
        working-directory: appsrc/android
        run: |
          chmod +x gradlew
          ./gradlew assembleDebug

      - name: Upload APK
        uses: actions/upload-artifact@v4
        with:
          name: SalesProBD-APK
          path: appsrc/android/app/build/outputs/apk/debug/app-debug.apk
          if-no-files-found: error
