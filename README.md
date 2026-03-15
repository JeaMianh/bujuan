<p align="center">
<img src="macos/Runner/Assets.xcassets/AppIcon.appiconset/app_icon_256.png" alt="app_icon_256" style="zoom: 50%;"/>
</p>
<h1 align="center">bujuan</h1>
<p align="center">一个支持Android、iOS、MacOS、Windows、Linux的三方网易云播放器。</p>

#### MacOS 

![](gif/desktop1.png)
![](gif/desktop2.png)
![](gif/desktop3.png)
![](gif/home.gif)



#### Android & IOS

| <img src="gif/phone1.jpg" alt="956" style="zoom:20%;" /> | <img src="gif/phone2.jpg" alt="954" style="zoom:20%;" /> | <img src="gif/phone3.jpg" alt="955" style="zoom:20%;" /> |
|----------------------------------------------------------|----------------------------------------------------------|----------------------------------------------------------|







## 功能介绍
- 采用flutter开发，可打包编译为所有平台。

- 网易云账号登录（手机号、二维码）。

- 每日推荐、私人Fm、电台、云盘。

- 歌手、专辑、搜索

- 去除无用功能，专注于用户音乐。





### 代码部署编译

#### 本地编译
```bash
flutter pub get
flutter build apk --release   # Android
flutter build ios              # iOS
flutter build macos            # macOS
```

---

### 在线自动构建 APK（GitHub Actions）

本项目已配置 GitHub Actions 工作流，可在 GitHub 上直接构建可安装的 APK，无需本地环境。

> **重要提示**：GitHub 规定，Actions 的手动触发按钮（Run workflow）只会在**默认分支**上存在工作流文件时才出现。请先将此 PR 合并到默认分支 `feature/new-ui`，之后按钮即可出现。

#### 方式一：手动触发构建（获取 APK）

1. 将此 PR 合并到 `feature/new-ui`
2. 打开仓库页面，点击顶部 **Actions** 标签
3. 在左侧选择 **Build APK**
4. 点击右侧 **Run workflow** → **Run workflow**（绿色按钮）
5. 等待构建完成（约 5~10 分钟），点击对应的运行记录
6. 在页面底部 **Artifacts** 区域下载 **bujuan-release-apk**（保留 30 天）

#### 方式二：发布正式版本（打 Tag 自动发布）

推送版本标签后，工作流会自动构建 APK 并创建 GitHub Release：

```bash
git tag v1.0.0
git push origin v1.0.0
```

之后在仓库 **Releases** 页面即可看到可下载的 APK 文件。

---

### 配置 APK 正式签名（可选，推荐用于发布）

> 默认情况下使用 debug 签名构建，已可正常安装。若需要正式签名（例如上架应用市场），请按以下步骤操作。

#### 第一步：生成签名 Keystore

在本地终端执行（需安装 JDK）：

```bash
keytool -genkey -v \
  -keystore release-keystore.jks \
  -keyalg RSA -keysize 2048 -validity 10000 \
  -alias bujuan \
  -storepass YOUR_KEYSTORE_PASSWORD \
  -keypass YOUR_KEY_PASSWORD
```

> ⚠️ 请将 `YOUR_KEYSTORE_PASSWORD` 和 `YOUR_KEY_PASSWORD` 替换为你自己设置的密码，并妥善保存 `release-keystore.jks` 文件，丢失后无法更新已发布的应用。

#### 第二步：将 Keystore 转换为 Base64

```bash
base64 -i release-keystore.jks | tr -d '\n'
```

复制输出的字符串备用。

#### 第三步：在 GitHub 仓库配置 Secrets

进入仓库 **Settings → Secrets and variables → Actions → New repository secret**，依次添加以下 4 个 Secret：

| Secret 名称        | 说明                                  |
|--------------------|---------------------------------------|
| `KEYSTORE_BASE64`  | 上一步 base64 编码后的 keystore 内容  |
| `KEYSTORE_PASSWORD`| Keystore 的密码（`-storepass`）       |
| `KEY_ALIAS`        | 密钥别名（`-alias`，如 `bujuan`）     |
| `KEY_PASSWORD`     | 密钥密码（`-keypass`）                |

配置完成后，下次构建时 CI 会自动使用正式签名打包 APK。
