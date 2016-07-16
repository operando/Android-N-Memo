# Android-N-Memo

Android N Memo


## Launcher Shortcuts API

* Nでは入らないらしい

https://developer.android.com/preview/support.html#dp4

```
As announced at Developer Preview 3, we’ve deferred the Launcher Shortcuts feature to a later release of Android. In Developer Preview 4, we’ve removed the Launcher Shortcuts APIs.

```

## FrameMetricsListener API

> You can use FrameMetricsListener to measure interaction-level UI performance in production, without a USB connection.


## android.provider.Settings

* ACTION_WEBVIEW_SETTINGS


## Data Saver

* https://developer.android.com/preview/features/data-saver.html


## Behavior Changes


### Project Svelte: Background Optimizations

Wifiやmobile dataなど接続が頻繁に変わる。

その度CONNECTIVITY_ACTION broadcastを登録しているアプリの処理が動く。

同様に ACTION_NEW_PICTURE and ACTION_NEW_VIDEO broadcastsなども頻繁に起きるよねー

なのでNでは以下のようにしまっせ！

*  CONNECTIVITY_ACTION broadcastsをmanifestに書いてても動かないよー
* アプリが実行中ならCONNECTIVITY_CHANGEは通知されるよー
 * 実行中というのはResume??
 * 詳しく調査してみる
*  ACTION_NEW_PICTURE or ACTION_NEW_VIDEO broadcastsを送信・受信できないよー

まあここらへんJobSchedulerあるから、それでなんとかしてねーという方針らしい

### File system permission changes

file:// のURIを使うとFileUriExposedExceptionになる

FileProviderで対応しろって話らしい

### Sharing Files Between Apps

fileシェアするのに file://使うなってことかな？

StrictModeに引っかかるようになるからねー


### Screen Zoom

Screenの密度を変えられるようになるよー

When the device density changes, the system notifies running apps in the following ways:

* API level 23以下の場合、すべてのBackgroundプロセスは強制終了する.
 * low-memoryの時と同じ感じでKillする
 * フォアグラウンドのプロセスはconfiguration changeが起きた感じになるよー
  * これどんな動作なのか調べてみる
* Nをtargetとしてる場合、フォアグラウンド・Backgroundのプロセスにconfiguration changeが通知される

まあほとんどのアプリがこれらの変更を気にする必要はないけど、以下気にしろって話

* 横幅 sw320dp向けのScreen Sizeで確認しろよ
* 画面密度に依存した値をキャッシュしてるならconfiguration changedで更新してねーBitmapとかresourcesから取り出した値とか
 * これは確かに気をつけないと。Bitmapは時に使ってるLibraryとかに依存するので気をつけないと。Resourceもだけど。
 * なんかnoteに書いてあるけどよくわかんねー
* Pixel使うな
 * 常考だろ


### Vision Settings in Setup Wizard

Nからsetup時に以下を設定できるようになったよ.

* Magnification gesture
* Font size
* Display size
* TalkBack

なにこれ、設定が簡単になってココらへんの変更で出るbugが今までより発生するかもしれんからSettings > Accessibilityで確認しろよ！って話？？怖い...


### Language and Locale

https://developer.android.com/preview/features/multilingual-support.html


## Key Developer Features

### Data Saver


## Developer Options

### WebViewの実装変更

* WebViewComponentで使用するChromeのバージョンを変えられる


### マルチプロセス WebView

## android-n-preview-1 to android-n-preview-2 AOSP changelog

http://www.androidpolice.com/android_aosp_changelogs/android-n-preview-1-to-android-n-preview-2-AOSP-changelog.html
