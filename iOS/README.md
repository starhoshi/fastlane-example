# fastlane iOS example

This is setting to run fastlane for ios.

# Requirements

## [Homebrew](https://brew.sh/)

```
$ brew install imagemagick
$ brew install graphicsmagick
```

## Environment Variables

```
ENV["DANGER_GITHUB_API_TOKEN"] = "YOUR_GITHUB_API_TOKEN"
ENV["FASTLANE_USER"] = "YOUR_APPLE_ID"
ENV["FASTLANE_PASSWORD"] = "YOUR_APPLE_PASSWORD"
ENV["DELIVER_PASSWORD"] = "YOUR_ITUNES_CONNECT_PASSWORD"
ENV["MATCH_PASSWORD"] = "YOUR_MATCH_PASSWORD"
ENV["BETA_MATCH_TYPE"] = "development or ad-hoc"
ENV["CRASHLYTICS_API_TOKEN"] = "YOUR_CRASHLYTICS_TOKEN"
ENV["CRASHLYTICS_BUILD_SECRET"] = "YOUR_CRASHLYTICS_BUILD_SECRET"
ENV["CRASHLYTICS_GROUPS"] = "YOUR_CRASHLYTICS_GROUPS"
ENV["SLACK_URL"] = "YOUR_SLACK_WEBHOOK_URL"
ENV["XCOV_WORKSPACE"] = "classi.xcworkspace"
ENV["XCOV_SCHEME"] = "ClassiTests"
ENV["XCOV_EXCLUDE_TARGETS"] = "GoogleToolboxForMac.framework,Rswift.framework"
ENV["SLACK_CHANNEL"] = "YOUR_SLACK_CHANNEL"
ENV["RELEASE_GYM_SCHEME"] = "YOUR_RELEASE_SCHEME"
ENV["XCODEPROJ"] = "classi.xcodeproj"
ENV["GITHUB_REPOSITORY"] = "classi/fastlane-example"
```

# Usage

## Test

```
$ bundle exec fastlane test
```

## Upload to crashlytics beta

```
$ bundle exec fastlane beta
```

## Upload to iTunes Connect

```
$ bundle exec fastlane release
```

### スクリーンショットの重複について

`deliver` はアップロード直後の検証で「まだ App Store Connect 側に見えない」
スクショを未アップロード扱いにして再送するため、同じ画像が二重に並ぶことがある。

`release` は `Deliverfile` の `skip_screenshots` が `false`（＝この実行でスクショを
上げた）ときだけ重複を確認し、**見つかったときだけ** `fix_screenshots` を呼ぶ。

`get_edit_app_store_version` のフィルタには `WAITING_FOR_REVIEW` が含まれるので、
審査に出した後でも重複の有無は読める。そのため重複が無ければ self reject せず、
審査の順番待ちをそのまま保てる。

重複していたときだけ、提出の取り下げ → 重複の削除 → 審査へ再提出まで自動で走る。
手動で流したいときは単体でも呼べる。

```
$ bundle exec fastlane fix_screenshots
```

## Create release branch

```
$ bundle exec fastlane release_branch version:2.0.0
```

