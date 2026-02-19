# Flutter CI/CD 自动化

## 概述

本文介绍 Flutter 项目的持续集成与持续部署（CI/CD）配置，实现自动化测试、构建和发布。

## CI/CD 工具对比

| 工具 | 特点 | 适用场景 |
|------|------|----------|
| GitHub Actions | 免费额度充足，配置简单 | 开源项目、小团队 |
| Codemagic | Flutter 官方推荐，专注移动端 | Flutter 项目首选 |
| Fastlane | 强大的发布工具链 | 需要精细控制发布流程 |
| Bitrise | 可视化配置，移动端友好 | 企业级项目 |

## GitHub Actions 配置

### 基础工作流

```yaml
# .github/workflows/flutter-ci.yml
name: Flutter CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  analyze-and-test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Flutter
        uses: subosito/flutter-action@v2
        with:
          flutter-version: '3.16.0'
          channel: 'stable'
          cache: true

      - name: Install dependencies
        run: flutter pub get

      - name: Analyze code
        run: flutter analyze

      - name: Check formatting
        run: dart format --set-exit-if-changed .

      - name: Run tests
        run: flutter test --coverage

      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          files: coverage/lcov.info
```

### 多平台构建

```yaml
# .github/workflows/build.yml
name: Build

on:
  push:
    branches: [main]
    tags:
      - 'v*'

jobs:
  build-android:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: subosito/flutter-action@v2
        with:
          flutter-version: '3.16.0'
          channel: 'stable'
          cache: true

      - uses: actions/setup-java@v3
        with:
          distribution: 'temurin'
          java-version: '17'

      - name: Build APK
        run: |
          flutter pub get
          flutter build apk --release

      - name: Build App Bundle
        run: flutter build appbundle --release

      - name: Upload APK
        uses: actions/upload-artifact@v3
        with:
          name: app-release
          path: build/app/outputs/flutter-apk/app-release.apk

  build-ios:
    runs-on: macos-latest
    steps:
      - uses: actions/checkout@v4

      - uses: subosito/flutter-action@v2
        with:
          flutter-version: '3.16.0'
          channel: 'stable'
          cache: true

      - name: Build iOS (no codesign)
        run: |
          flutter pub get
          flutter build ios --release --no-codesign

      - name: Upload IPA
        uses: actions/upload-artifact@v3
        with:
          name: ios-build
          path: build/ios/iphoneos/

  build-web:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: subosito/flutter-action@v2
        with:
          flutter-version: '3.16.0'
          channel: 'stable'
          cache: true

      - name: Build Web
        run: |
          flutter pub get
          flutter build web --release

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./build/web
```

## Fastlane 配置

### 安装 Fastlane

```bash
# macOS
brew install fastlane

# 或使用 gem
gem install fastlane

# 初始化
fastlane init
```

### Android 配置

```ruby
# android/fastlane/Fastfile
default_platform(:android)

platform :android do
  desc "Build release APK"
  lane :build_apk do
    gradle(task: "assembleRelease")
  end

  desc "Build release App Bundle"
  lane :build_bundle do
    gradle(task: "bundleRelease")
  end

  desc "Deploy to Play Store"
  lane :deploy do
    build_bundle
    upload_to_play_store(
      track: 'internal',
      aab: '../build/app/outputs/bundle/release/app-release.aab'
    )
  end

  desc "Deploy to Play Store Beta"
  lane :beta do
    build_bundle
    upload_to_play_store(
      track: 'beta',
      aab: '../build/app/outputs/bundle/release/app-release.aab'
    )
  end
end
```

### iOS 配置

```ruby
# ios/fastlane/Fastfile
default_platform(:ios)

platform :ios do
  desc "Build iOS app"
  lane :build do
    build_app(
      workspace: "Runner.xcworkspace",
      scheme: "Runner",
      export_method: "ad-hoc"
    )
  end

  desc "Build and upload to TestFlight"
  lane :beta do
    build_app(
      workspace: "Runner.xcworkspace",
      scheme: "Runner",
      export_method: "app-store"
    )
    upload_to_testflight(
      skip_waiting_for_build_processing: true
    )
  end

  desc "Deploy to App Store"
  lane :release do
    build_app(
      workspace: "Runner.xcworkspace",
      scheme: "Runner",
      export_method: "app-store"
    )
    upload_to_app_store(
      force: true,
      submit_for_review: true,
      automatic_release: true
    )
  end
end
```

## Codemagic 配置

### codemagic.yaml

```yaml
# codemagic.yaml
workflows:
  android-workflow:
    name: Android Workflow
    max_build_duration: 30
    environment:
      flutter: stable
      xcode: latest
      cocoapods: default
    scripts:
      - name: Get dependencies
        script: |
          flutter pub get
      - name: Run tests
        script: |
          flutter test
      - name: Build APK
        script: |
          flutter build apk --release
    artifacts:
      - build/**/outputs/**/*.apk
    publishing:
      email:
        recipients:
          - user@example.com
      google_play:
        credentials: $GCLOUD_SERVICE_ACCOUNT_CREDENTIALS
        track: internal

  ios-workflow:
    name: iOS Workflow
    max_build_duration: 60
    environment:
      flutter: stable
      xcode: latest
      cocoapods: default
    scripts:
      - name: Get dependencies
        script: |
          flutter pub get
      - name: Run tests
        script: |
          flutter test
      - name: Build iOS
        script: |
          flutter build ios --release --no-codesign
    artifacts:
      - build/ios/iphoneos/**/*.app
    publishing:
      app_store_connect:
        api_key: $APP_STORE_CONNECT_PRIVATE_KEY
        key_id: $APP_STORE_CONNECT_KEY_IDENTIFIER
        issuer_id: $APP_STORE_CONNECT_ISSUER_ID
```

## 环境变量管理

### GitHub Secrets

在 GitHub 仓库 Settings → Secrets 中配置：

```
# Android
GOOGLE_PLAY_SERVICE_ACCOUNT_JSON

# iOS
APP_STORE_CONNECT_API_KEY
APP_STORE_CONNECT_ISSUER_ID
APP_STORE_CONNECT_KEY_ID

# 签名
ANDROID_KEYSTORE_BASE64
ANDROID_KEYSTORE_PASSWORD
ANDROID_KEY_ALIAS
ANDROID_KEY_PASSWORD

IOS_CERTIFICATE_BASE64
IOS_CERTIFICATE_PASSWORD
IOS_PROVISIONING_PROFILE_BASE64
```

### 使用环境变量

```yaml
# .github/workflows/deploy.yml
- name: Decode keystore
  run: |
    echo "${{ secrets.ANDROID_KEYSTORE_BASE64 }}" | base64 --decode > android/app/keystore.jks

- name: Create key.properties
  run: |
    cat > android/key.properties << EOF
    storePassword=${{ secrets.ANDROID_KEYSTORE_PASSWORD }}
    keyPassword=${{ secrets.ANDROID_KEY_PASSWORD }}
    keyAlias=${{ secrets.ANDROID_KEY_ALIAS }}
    storeFile=keystore.jks
    EOF
```

## 版本管理

### 自动更新版本号

```yaml
# .github/workflows/release.yml
name: Release

on:
  workflow_dispatch:
    inputs:
      version:
        description: 'Version (e.g., 1.2.0)'
        required: true

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Update version
        run: |
          # 更新 pubspec.yaml
          sed -i 's/^version: .*/version: ${{ github.event.inputs.version }}+1/' pubspec.yaml

      - name: Commit version update
        run: |
          git config --local user.email "action@github.com"
          git config --local user.name "GitHub Action"
          git commit -am "chore: bump version to ${{ github.event.inputs.version }}"
          git push

      - name: Create tag
        run: |
          git tag v${{ github.event.inputs.version }}
          git push origin v${{ github.event.inputs.version }}
```

## 测试覆盖率

### 生成覆盖率报告

```yaml
# .github/workflows/test.yml
- name: Run tests with coverage
  run: flutter test --coverage

- name: Generate coverage report
  run: |
    dart pub global activate coverage
    format_coverage --lcov --in=coverage --out=coverage/lcov.info

- name: Upload to Codecov
  uses: codecov/codecov-action@v3
  with:
    files: coverage/lcov.info
    fail_ci_if_error: true
```

## 发布清单

### 发布前检查

```yaml
# .github/workflows/pre-release.yml
- name: Pre-release checks
  run: |
    echo "Checking Flutter version..."
    flutter --version

    echo "Checking dependencies..."
    flutter pub get

    echo "Running analyzer..."
    flutter analyze

    echo "Running tests..."
    flutter test

    echo "Checking for TODO comments..."
    ! grep -r "TODO" lib/

    echo "Checking for debug prints..."
    ! grep -r "debugPrint" lib/
    ! grep -r "print(" lib/

    echo "Checking for hardcoded secrets..."
    ! grep -rE "(api_key|secret|password|token)" lib/
```

## 最佳实践

### 1. 缓存依赖

```yaml
- uses: subosito/flutter-action@v2
  with:
    flutter-version: '3.16.0'
    channel: 'stable'
    cache: true
    cache-key: 'flutter-:os:-:channel:-:version:-:arch:-:hash'
    cache-path: '${{ runner.tool_cache }}/flutter/:channel:-:version:-:arch:'
```

### 2. 矩阵构建

```yaml
strategy:
  matrix:
    flutter-version: ['3.10.0', '3.16.0']
    channel: ['stable', 'beta']
```

### 3. 条件执行

```yaml
- name: Deploy to Production
  if: startsWith(github.ref, 'refs/tags/v')
  run: |
    # 只在 tag 推送时部署
```

---

*最后更新: 2026-02-19*
