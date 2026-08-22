# fastlane Android example

Android の配信設定。各アプリの `android/fastlane/Fastfile` から
`import_from_git` で読み込んで使う。

```ruby
fastlane_version "2.19.2"

import_from_git(
  url: 'https://github.com/starhoshi/fastlane-example',
  path: 'Android/fastlane/Fastfile'
)
```

アプリ側の `android/fastlane/Appfile` には `package_name` だけを書く。

## レーン

`deploy` の1本だけ。**ビルドして Play にアップロードする。**

```
$ bundle exec fastlane deploy
```

## 環境変数

秘密情報は sops で暗号化して `sops exec-env` で子プロセスに渡す。
**平文でファイルに書いたりログに出したりしない。**

| 変数 | 既定 | 用途 |
| --- | --- | --- |
| `SUPPLY_JSON_KEY_DATA` | （必須） | Play Developer API のサービスアカウント JSON。中身をそのまま入れる |
| `ANDROID_BUILD_COMMAND` | Flutter のビルド | ビルドコマンド。下記参照 |
| `ANDROID_VERSION_CODE` | epoch 秒 | versionCode。既定のまま使うのが安全 |
| `SUPPLY_TRACK` | `production` | `internal` / `alpha` / `beta` / `production` |
| `SUPPLY_AAB` | `../build/app/outputs/bundle/release/app-release.aab` | aab のパス（`android/` からの相対） |
| `SUPPLY_RELEASE_STATUS` | `draft` | `draft` なら Play 上で下書きのまま止まる |
| `SUPPLY_SKIP_UPLOAD_METADATA` | `false` | `'true'` という**文字列**で渡す |
| `SUPPLY_SKIP_UPLOAD_CHANGELOGS` | `false` | 同上 |
| `SUPPLY_SKIP_UPLOAD_IMAGES` | `false` | 同上 |
| `SUPPLY_SKIP_UPLOAD_SCREENSHOTS` | `false` | 同上 |

### ANDROID_BUILD_COMMAND

**未設定なら Flutter。** 既存の Flutter アプリは何も設定しなくてよい。

```
（未設定）                        flutter build appbundle --release --build-number=<epoch秒>
"./gradlew bundleRelease"        素の Android / Expo など
""（空文字）                      ビルドしない。すでに出来ている aab を上げるだけ
```

空文字が要るのは、**署名鍵の復元など前処理があるアプリ**のため。
そういうアプリはビルドを自分のタスク側でやって、fastlane にはアップロードだけさせる。

### versionCode に epoch 秒を使う理由

Play は「前回より大きい versionCode」を要求する。手で採番すると必ずどこかで
重複させて、アップロードのたびに詰まる。epoch 秒なら採番表を持たずに必ず増える。
2038年まで Integer に収まる。

## 注意

- `upload_to_play_store` は**アプリが Play に一度も登録されていないと失敗する。**
  最初の1回だけは Play Console から手でアップロードして枠を作ること
- `release_status` の既定は `draft` なので、流しても勝手に公開はされない。
  公開は Play Console で人間が押す
