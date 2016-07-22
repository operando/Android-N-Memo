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
 * adb shell am start -a android.settings.WEBVIEW_SETTINGS
 * I/ActivityManager( 4573): START u0 {act=android.settings.WEBVIEW_SETTINGS flg=0x10000000 cmp=com.android.settings/.WebViewImplementation} from uid 2000 on display 0
 * Chrome Devとか入ってても選択肢に出ててこない
 * 何を基準にしてるのかよくわからん...
 * Android N Preview 5になったら直った


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

* https://developer.android.com/preview/features/data-saver.html
* Data Saverの設定画面にIntentで飛べるかどうか
 * Actionはもちろん提供されてないし、Fragmentしかないので無理そう。source公開されたら最終判断できそう
* Data Saverのホワイトリストがどこで管理されているのか
 * netpolicy.xml??
 * ようわからん

logとか

```
// Data SaverのFragment
D/SubSettings( 5785): Launching fragment com.android.settings.datausage.DataSaverSummary

// ホワイトリストのFragment
D/SubSettings( 5785): Launching fragment com.android.settings.datausage.UnrestrictedDataAccess

// 各アプリのdata usageのFragment
// 下のIntentからわかるようにAppDataUsageActivityみたいに外部から呼び出される入り口もある
D/SubSettings( 5785): Launching fragment com.android.settings.datausage.AppDataUsage

// Intent(Settings.ACTION_IGNORE_BACKGROUND_DATA_RESTRICTIONS_SETTINGS, Uri.parse("package:" + getPackageName()))で呼ばれる先
START u0 {act=android.settings.IGNORE_BACKGROUND_DATA_RESTRICTIONS_SETTINGS dat=package:com.os.operando.datasaver.sample cmp=com.android.settings/.datausage.AppDataUsageActivity} from uid 10139 on display 0


// whitelist on
I/NetworkPolicy( 4573): adding uid 10139 to restrict background whitelist

// whitelist off
I/NetworkPolicy( 4573): removing uid 10139 from restrict background whitelist

// Data Saver on
D/ConnectivityService( 4573): onRestrictBackgroundChanged(true): disabling tethering
```

```
// off
07-18 09:01:25.530 I/sysui_histogram( 5785): [datausage.DataSaverSummary/switch_bar|false,1]
07-18 09:01:25.671 I/sysui_action( 5785): [394,0]

// on
07-18 09:02:26.371 I/sysui_action( 5785): [394,1]
```


### Direct Boot

* https://developer.android.com/preview/features/direct-boot.html
* Device protected storageってどこが保存先になるのか??


## Developer Options

### WebViewの実装変更

* WebViewComponentで使用するChromeのバージョンを変えられる


### マルチプロセス WebView

## android-n-preview-1 to android-n-preview-2 AOSP changelog

http://www.androidpolice.com/android_aosp_changelogs/android-n-preview-1-to-android-n-preview-2-AOSP-changelog.html


## SDK

### android.text.Html

https://developer.android.com/sdk/api_diff/24/changes/android.text.Html.html

deprecatedになって新しいAPIが生えてる

* toHtml deprecated
* fromHtml deprecated


## ねこあつめ


07-21 22:48:24.535 I/ActivityManager( 4741): START u0 {act=android.intent.action.MAIN cmp=android/com.android.internal.app.PlatLogoActivity} from uid 1000 on display 0
07-21 22:48:24.549 I/InputDispatcher( 4741): Dropping event because there is no touchable window at (524, 822).
07-21 22:48:24.602 D/OpenGLRenderer( 9202): endAllActiveAnimators on 0x78e123c800 (RippleDrawable) with handle 0x78c5b548c0
07-21 22:48:24.611 I/ActivityManager( 4741): Displayed android/com.android.internal.app.PlatLogoActivity: +67ms (total +4m20s501ms)


07-21 22:47:43.145 I/ActivityManager( 4741): START u0 {act=android.intent.action.MAIN cat=[com.android.internal.category.PLATLOGO] flg=0x10808000 cmp=com.android.egg/.neko.NekoActivationActivity} from uid 1000 on display 0
07-21 22:47:43.176 W/InputMethodManagerService( 4741): Window already focused, ignoring focus gain of: com.android.internal.view.IInputMethodClient$Stub$Proxy@1b68756 attribute=null, token = android.os.BinderProxy@895a650
07-21 22:47:45.729 I/WindowManager( 4741): Destroying surface Surface(name=Toast) called by com.android.server.wm.WindowStateAnimator.destroySurface:2014 com.android.server.wm.WindowStateAnimator.destroySurfaceLocked:881 com.android.server.wm.WindowState.destroyOrSaveSurface:2073 com.android.server.wm.WindowSurfacePlacer.performSurfacePlacementInner:429 com.android.server.wm.WindowSurfacePlacer.performSurfacePlacementLoop:232 com.android.server.wm.WindowSurfacePlacer.performSurfacePlacement:180 com.android.server.wm.WindowManagerService$H.handleMessage:8079 android.os.Handler.dispatchMessage:102
07-21 22:47:53.217 I/InputReader( 4741): Reconfiguring input devices.  changes=0x00000010
07-21 22:47:53.245 D/RegisteredNfcFServicesCache( 7161): Service unchanged, not updating


// menuおした時
07-21 22:52:49.008 D/NekoTile( 9584): showNekoDialog


// Bitsを押した時
07-21 22:53:18.613 W/JobInfo ( 9584): Specified interval for 42 is +13m15s250ms. Clamped to +15m0s0ms
07-21 22:53:18.613 V/NekoService( 9584): A cat will visit in 795250ms: (job:42/com.android.egg/.neko.NekoService)
07-21 22:53:18.665 I/MicroDetectionWorker( 7301): Micro detection mode: [mDetectionMode: [1]].
07-21 22:53:18.688 I/MicroRecognitionRunner( 7301): Starting detection.
07-21 22:53:18.689 I/MicrophoneInputStream( 7301): mic_starting com.google.android.apps.gsa.speech.audio.ai@67cc6b
07-21 22:53:18.691 I/AudioFlinger( 3618): AudioFlinger's thread 0xeee83240 ready to run
07-21 22:53:18.697 I/SoundTriggerHwService::Module( 3618): void android::SoundTriggerHwService::Module::onCallbackEvent(const sp<android::SoundTriggerHwService::CallbackEvent> &) mClient == 0
07-21 22:53:18.700 I/MicrophoneInputStream( 7301): mic_started com.google.android.apps.gsa.speech.audio.ai@67cc6b
07-21 22:53:18.710 E/audio_route( 3618): unable to find path 'set-capture-format-default'
07-21 22:53:18.710 D/sound_trigger_platform( 3618): platform_stdev_check_and_update_concurrency: concurrency active 0, tx 1, rx 0, concurrency session_allowed 0
07-21 22:53:18.782 I/MicroDetectionWorker( 7301): onReady
07-21 22:53:18.843 I/WindowManager( 4741): Destroying surface Surface(name=) called by com.android.server.wm.WindowStateAnimator.destroySurface:2014 com.android.server.wm.WindowStateAnimator.destroySurfaceLocked:881 com.android.server.wm.WindowState.destroyOrSaveSurface:2073 com.android.server.wm.WindowSurfacePlacer.performSurfacePlacementInner:429 com.android.server.wm.WindowSurfacePlacer.performSurfacePlacementLoop:232 com.android.server.wm.WindowSurfacePlacer.performSurfacePlacement:180 com.android.server.wm.WindowManagerService$H.handleMessage:8079 android.os.Handler.dispatchMessage:102


// Bitsのクイック設定ツールを押した時
// jobschedulerで動いてる！！
07-21 22:55:45.098 V/NekoService( 9584): Canceling job

// Fishを押した時
07-21 23:00:23.640 V/NekoService( 9584): A cat will visit in 1696137ms: (job:42/com.android.egg/.neko.NekoService)
// Fishのクイック設定ツールを押した時
07-21 22:55:45.098 V/NekoService( 9584): Canceling job


// Chicken
07-21 23:02:45.271 V/NekoService( 9584): A cat will visit in 4474198ms: (job:42/com.android.egg/.neko.NekoService)


// Treat
07-21 23:03:30.389 W/JobInfo ( 9584): Specified flex for 42 is +5m0s0ms. Clamped to +7m3s284ms
07-21 23:03:30.389 V/NekoService( 9584): A cat will visit in 8465687ms: (job:42/com.android.egg/.neko.NekoService)


adb shell dumpsys jobscheduler com.android.egg



// 通知タップ
07-22 05:53:59.621 I/ActivityManager( 4741): START u0 {act=android.intent.action.MAIN flg=0x10000000 cmp=com.android.egg/.neko.NekoLand} from uid 10140 on display 0
07-22 05:53:59.650 E/ActivityManager( 4741): applyOptionsLocked: Unknown animationType=0
07-22 05:53:59.879 I/ActivityManager( 4741): Displayed com.android.egg/.neko.NekoLand: +229ms
07-22 05:54:00.073 I/WindowManager( 4741): Destroying surface Surface(name=Starting com.android.egg) called by com.android.server.wm.WindowStateAnimator.destroySurface:2014 com.android.server.wm.WindowStateAnimator.destroySurfaceLocked:881 com.android.server.wm.WindowState.destroyOrSaveSurface:2073 com.android.server.wm.AppWindowToken.destroySurfaces:363 com.android.server.wm.WindowStateAnimator.finishExit:565 com.android.server.wm.WindowStateAnimator.stepAnimationLocked:491 com.android.server.wm.WindowAnimator.updateWindowsLocked:303 com.android.server.wm.WindowAnimator.animateLocked:704
07-22 05:54:00.162 D/PhoneStatusBar( 6268): disable: < expand icons* alerts system_info* back home recent clock search quick_settings >
07-22 05:54:00.466 I/ActivityManager( 4741): Killing 16871:com.google.android.keep/u0a82 (adj 906): empty #17
07-22 05:54:00.475 I/WindowManager( 4741): Destroying surface Surface(name=com.google.android.googlequicksearchbox/com.google.android.launcher.GEL) called by com.android.server.wm.WindowStateAnimator.destroySurface:2014 com.android.server.wm.WindowStateAnimator.destroySurfaceLocked:881 com.android.server.wm.WindowState.destroyOrSaveSurface:2073 com.android.server.wm.AppWindowToken.destroySurfaces:363 com.android.server.wm.AppWindowToken.notifyAppStopped:389 com.android.server.wm.WindowManagerService.notifyAppStopped:4456 com.android.server.am.ActivityStack.activityStoppedLocked:1252 com.android.server.am.ActivityManagerService.activityStopped:6873

// 通知が来る時
07-22 06:07:40.367 I/ActivityManager( 4741): Start proc 19395:com.android.egg/u0a140 for service com.android.egg/.neko.NekoService
07-22 06:07:40.414 W/System  (19395): ClassLoader referenced unknown path: /system/app/EasterEgg/lib/arm64
07-22 06:07:40.435 V/NekoService(19395): Starting job: android.app.job.JobParameters@d24d20b
07-22 06:07:40.517 V/NekoService(19395): A cat has returned: Cat #195
07-22 06:07:40.609 V/NekoService(19395): Canceling job


* adb shell pm dump com.android.egg
* adb shell am start -n com.android.egg/.neko.NekoLand

## YadaYada


* /system/app/YadaYada/YadaYada.apk
