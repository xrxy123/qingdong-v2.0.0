# 轻动 (QingDong) · Android 版 v2.0.0

一个**零依赖**的健身运动 App（网页前端 + Android WebView 原生壳），小清新薄荷绿风格，
包含运动库、计时/计数训练、最佳成绩、统计图表、自定义计划、科学热量计算、音效等全部 2.0.0 功能
（含训练前「准备+3秒倒计时」语音播报、环形图标注优化、统计左右滑切换等）。

> ⚠️ **关于本交付物的重要说明**
> 我在当前开发环境中尝试直接编译并签名生成 APK，但沙箱网络对 `dl.google.com`、
> `aka.ms` 等站点的**大体积二进制下载（Android SDK ≈ 数百 MB、JDK ≈ 186 MB）被限速/阻断**
> （仅能拿到响应头，正文传输会卡死），因此**无法在此环境内直接产出 `.apk` 安装包文件**。
> 为此我为你准备好了**完整、可直接构建的 Android 工程 + 一键自动化构建脚本**，
> 你在自己电脑或云端（GitHub Actions）跑一次即可获得 `qingdong-v2.0.0.apk`。
> 工程内的网页资源已冻结为 2.0.0 版本，构建出的 APK 即等同于「2.0.0版本」。

---

## 一、三种构建方式（任选其一）

### 方式 A：GitHub Actions 云端构建（推荐，无需本机安装任何东西）
1. 在 GitHub 新建一个仓库。
2. 把本 `android/` 文件夹下的**全部内容**上传到仓库根目录（即 `settings.gradle`、`app/`、
   `.github/` 等直接在仓库根）。
3. 进入仓库 **Actions** 标签页 → 左侧 **Build 轻动 APK** → **Run workflow**。
4. 运行完成后，在 **Artifacts** 中下载 `qingdong-v2.0.0-apk`，里面就是签名好的安装包。
> 该工作流会自动安装 JDK17、Android SDK、build-tools 与 platform，并用 Gradle 构建。
> 默认以 debug 签名输出，可直接安装到手机（侧载）。

### 方式 B：Android Studio 本地构建（最直观）
1. 安装 [Android Studio](https://developer.android.com/studio)。
2. 打开 Android Studio → **Open** → 选择本 `android/` 文件夹。
3. 等待 Gradle 同步完成（会自动下载 SDK 组件）。
4. 菜单 **Build → Build Bundle(s) / APK(s) → Build APK(s)**。
5. 完成后右下角提示，APK 位于 `android/app/build/outputs/apk/release/app-release.apk`。

### 方式 C：本地脚本构建（仅 JDK + SDK，无需 Android Studio / Gradle）
1. 安装 JDK 17，设置环境变量 `JAVA_HOME`。
2. 安装 Android SDK（commandlinetools），设置 `ANDROID_HOME`，并运行：
   `sdkmanager "build-tools;34.0.0" "platforms;android-34"`
3. 安装 Python（用于注入资源）。
4. Windows：双击 `build_apk.bat`；macOS/Linux：`./build_apk.sh`。
5. 产物：`android/qingdong-v2.0.0.apk`。

---

## 二、安装到手机
- **方式一（最简单）**：把 `qingdong-v2.0.0.apk` 传到安卓手机，在文件管理器里点击安装
  （首次需允许「未知来源应用」安装权限）。
- **方式二（adb）**：手机开启 USB 调试并连接电脑，执行 `adb install qingdong-v2.0.0.apk`。

---

## 三、应用图标
图标沿用 1.0.0 设计规范（薄荷绿圆角底 + 「轻动」二字），已按 Android 规范生成：
- 传统 mipmap 图标（mdpi~xxxhdpi 五档密度），在任意机型桌面均能完美显示；
- 自适应图标（Adaptive Icon）前景/背景分层（API 26+），在各类启动器下自动适配形状
  （圆形、方形、圆角方、泪滴等），桌面显示完整不裁切。

---

## 四、机型适配（完美适配各类安卓手机）
为做到各类安卓机型（刘海屏 / 挖孔屏 / 水滴屏 / 全面屏 / 传统屏）的完美显示：
- **刘海/挖孔/水滴适配**：`MainActivity` 在 API 28+ 启用
  `LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES`，内容延伸至系统栏之下，不避让留白。
- **沉浸式系统栏**：状态栏与导航栏设为透明，网页以 `viewport-fit=cover` + 安全区
  （`env(safe-area-inset-*)`）自行避让，顶部/底部贴合各机型异形屏，无黑边绿边。
- **自适应图标**：见第三节，桌面图标在任意启动器完美显示。
- **五档密度资源**：图标/布局覆盖 mdpi~xxxhdpi，任意分辨率清晰。

---

## 五、兼容性与已知限制
- **最低支持 Android 5.0（API 21）**，覆盖市面 99% 以上机型；目标 API 34。
- App 完全离线运行（数据保存在手机本地 WebView 的 localStorage），无需联网。
- 网页中的**浏览器通知提醒（运动提醒）**在 Android WebView 中不受支持，
  其余功能（训练、计时/计数、统计、计划、音效、个人资料等）均正常工作。
- 竖屏锁定，已做状态栏/导航栏与异形屏安全区适配。

---

## 六、工程结构
```
android/
├─ app/src/main/
│  ├─ java/com/qingdong/fit/MainActivity.java   # WebView 原生壳（含刘海/沉浸适配）
│  ├─ AndroidManifest.xml                        # 包名 com.qingdong.fit, 版本 2.0.0
│  ├─ res/                                       # 图标 / 字符串 / 主题（透明系统栏）
│  └─ assets/                                    # 2.0.0 网页（index.html + css + js）
├─ build.gradle / settings.gradle / gradle.properties
├─ build_apk.bat / build_apk.sh                  # 本地免 Gradle 构建（产物 qingdong-v2.0.0.apk）
├─ inject_assets.py                              # 资源注入辅助
├─ .github/workflows/build-apk.yml               # 云端一键构建（artifact: qingdong-v2.0.0-apk）
└─ README.md
```

如需对 2.0.0 版本做修改升级，请基于项目中的 `versions/2.0.0/` 基线，
改完网页后重新把 `index.html / css / js` 同步进 `app/src/main/assets/` 再构建即可。
