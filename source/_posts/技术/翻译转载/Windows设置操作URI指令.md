---
title: Windows设置操作URI指令
date: 2024/03/31 19:45:42
updated: 2024/03/31 19:45:42
categories:
- [技术, 翻译转载]
tags:
- 转载
- Windows
---

## ms-settings: URI 方案参考

以下部分描述了用于打开“设置”应用的不同页面的各类 ms-settings URI：

- [帐户](https://learn.microsoft.com/zh-cn/windows/uwp/launch-resume/launch-settings-app#accounts)
- [应用](https://learn.microsoft.com/zh-cn/windows/uwp/launch-resume/launch-settings-app#apps)
- [控制中心](https://learn.microsoft.com/zh-cn/windows/uwp/launch-resume/launch-settings-app#control-center)
- [Cortana](https://learn.microsoft.com/zh-cn/windows/uwp/launch-resume/launch-settings-app#cortana)
- [设备](https://learn.microsoft.com/zh-cn/windows/uwp/launch-resume/launch-settings-app#devices)
- [轻松访问](https://learn.microsoft.com/zh-cn/windows/uwp/launch-resume/launch-settings-app#ease-of-access)
- [附加信息](https://learn.microsoft.com/zh-cn/windows/uwp/launch-resume/launch-settings-app#extras)
- [家庭组](https://learn.microsoft.com/zh-cn/windows/uwp/launch-resume/launch-settings-app#family-group)
- [游戏](https://learn.microsoft.com/zh-cn/windows/uwp/launch-resume/launch-settings-app#gaming)
- [混合现实](https://learn.microsoft.com/zh-cn/windows/uwp/launch-resume/launch-settings-app#mixed-reality)
- [网络和 Internet](https://learn.microsoft.com/zh-cn/windows/uwp/launch-resume/launch-settings-app#network-and-internet)
- [个性化设置](https://learn.microsoft.com/zh-cn/windows/uwp/launch-resume/launch-settings-app#personalization)
- [电话](https://learn.microsoft.com/zh-cn/windows/uwp/launch-resume/launch-settings-app#phone)
- [隐私](https://learn.microsoft.com/zh-cn/windows/uwp/launch-resume/launch-settings-app#privacy)
- [搜索](https://learn.microsoft.com/zh-cn/windows/uwp/launch-resume/launch-settings-app#search)
- [Surface Hub](https://learn.microsoft.com/zh-cn/windows/uwp/launch-resume/launch-settings-app#surface-hub)
- [系统](https://learn.microsoft.com/zh-cn/windows/uwp/launch-resume/launch-settings-app#system)
- [时间和语言](https://learn.microsoft.com/zh-cn/windows/uwp/launch-resume/launch-settings-app#time-and-language)
- [更新和安全](https://learn.microsoft.com/zh-cn/windows/uwp/launch-resume/launch-settings-app#update-and-security)
- [用户帐户](https://learn.microsoft.com/zh-cn/windows/uwp/launch-resume/launch-settings-app#user-accounts)



### 帐户

| “设置”页面         | URI                                                          |
| :----------------- | :----------------------------------------------------------- |
| 访问工作单位或学校 | ms-settings:workplace                                        |
| 电子邮件和应用帐户 | ms-settings:emailandaccounts                                 |
| 家人和其他人       | ms-settings:otherusers                                       |
| 设置展台           | ms-settings:assignedaccess                                   |
| 登录选项           | ms-settings:signinoptions ms-settings:signinoptions-dynamiclock |
| 同步你的设置       | ms-settings:sync ms-settings:backup（已在 Windows 11 中弃用的备份页） |
| Windows Hello 设置 | ms-settings:signinoptions-launchfaceenrollment ms-settings:signinoptions-launchfingerprintenrollment |
| 你的信息           | ms-settings:yourinfo                                         |



### 应用

| “设置”页面     | URI                                                          |
| :------------- | :----------------------------------------------------------- |
| 应用和功能     | ms-settings:appsfeatures                                     |
| 应用功能       | ms-settings:appsfeatures-app（应用的重置、管理加载项和可下载内容等）  若要使用 URI 访问此页面，请使用 ms-settings:appsfeatures-app URI 并传递应用的“包系列名称”的可选参数。 |
| 网站应用       | ms-settings:appsforwebsites                                  |
| 默认应用       | ms-settings:defaultapps（**在 Windows 11 版本 21H2（具有 2023-04 累积更新）或 22H2（具有 2023-04 累积更新）或更高版本中引入的行为。**） 使用应用的 URI 转义名称以下列格式追加查询字符串参数，以直接启动该应用的默认设置页：  - registeredAppMachine=<应用的 URI 转义的每台计算机安装名> - registeredAppUser=<应用的 URI 转义的每个用户安装名> - registeredAUMID=<URI 转义的应用程序用户模型 ID>  有关详细信息，请参阅[启动默认应用设置页](https://learn.microsoft.com/zh-cn/windows/uwp/launch-resume/launch-default-apps-settings)。 |
| 默认浏览器设置 | ms-settings:defaultbrowsersettings（**在 Windows 11 中弃用**） |
| 管理可选功能   | ms-settings:optionalfeatures                                 |
| 脱机地图       | ms-settings:maps ms-settings:maps-downloadmaps（下载地图）   |
| 启动应用       | ms-settings:startupapps                                      |
| 视频播放       | ms-settings:videoplayback                                    |



### 控制中心

| “设置”页面 | URI                       |
| :--------- | :------------------------ |
| 控制中心   | ms-settings:controlcenter |



### Cortana

| “设置”页面               | URI                                                          |
| :----------------------- | :----------------------------------------------------------- |
| 跨越我的设备使用 Cortana | ms-settings:cortana-notifications                            |
| 更多详细信息             | ms-settings:cortana-moredetails                              |
| 权限和历史记录           | ms-settings:cortana-permissions                              |
| 搜索 Windows             | ms-settings:cortana-windowssearch                            |
| 与 Cortana 交谈          | ms-settings:cortana-language ms-settings:cortana ms-settings:cortana-talktocortana |

 备注

将电脑设置为 Cortana 当前不可用或 Cortana 已禁用的区域时，桌面上的此“设置”部分称为“搜索”。 在这种情况下，不会列出特定于 Cortana 的页面（“跨设备的 Cortana”和“与 Cortana 交谈”）。


### 设备

| “设置”页面           | URI                                                          |
| :------------------- | :----------------------------------------------------------- |
| 自动播放             | ms-settings:autoplay                                         |
| Bluetooth            | ms-settings:bluetooth                                        |
| 连接的设备数         | ms-settings:connecteddevices                                 |
| 默认相机             | ms-settings:camera（已在 Windows 10 版本 1809 及更高版本中弃用的行为） |
| 相机设置             | ms-settings:camera（已在 Windows 11 版本 22000 及更高版本中引入的行为）将查询字符串参数 `cameraId` 集追加到相机设备的 URI 转义符号链接名称，以直接启动该相机的设置。 有关详细信息，请参阅[启动相机设置页](https://learn.microsoft.com/zh-cn/windows/uwp/audio-video-camera/launch-camera-settings)。 |
| 鼠标和触摸板         | ms-settings:mousetouchpad（仅在具有触控板的设备上可用的触控板设置） |
| 触控笔和 Windows Ink | ms-settings:pen                                              |
| 打印机和扫描仪       | ms-settings:printers                                         |
| 触控                 | ms-settings:devices-touch                                    |
| 触摸板               | ms-settings:devices-touchpad（仅当存在触控板硬件时才可用）   |
| 文本建议             | ms-settings:devicestyping-hwkbtextsuggestions                |
| Typing               | ms-settings:typing                                           |
| USB                  | ms-settings:usb                                              |
| 滚轮                 | ms-settings:wheel（仅当拨号配对时可用）                      |
| 你的手机             | ms-settings:mobile-devices                                   |



### 轻松访问

| “设置”页面     | URI                                                          |
| :------------- | :----------------------------------------------------------- |
| 音频           | ms-settings:easeofaccess-audio                               |
| 隐藏式字幕     | ms-settings:easeofaccess-closedcaptioning                    |
| 颜色筛选器     | ms-settings:easeofaccess-colorfilter ms-settings:easeofaccess-colorfilter-adaptivecolorlink ms-settings:easeofaccess-colorfilter-bluelightlink |
| 显示           | ms-settings:easeofaccess-display                             |
| 眼球控制       | ms-settings:easeofaccess-eyecontrol                          |
| 字体           | ms-settings:fonts                                            |
| 高对比度       | ms-settings:easeofaccess-highcontrast                        |
| 键盘           | ms-settings:easeofaccess-keyboard                            |
| 放大镜         | ms-settings:easeofaccess-magnifier                           |
| 鼠标           | ms-settings:easeofaccess-mouse                               |
| 鼠标指针和触控 | ms-settings:easeofaccess-mousepointer                        |
| 讲述人         | ms-settings:easeofaccess-narrator ms-settings:easeofaccess-narrator-isautostartenabled |
| 语音           | ms-settings:easeofaccess-speechrecognition                   |
| 文本光标       | ms-settings:easeofaccess-cursor                              |
| 视觉效果       | ms-settings:easeofaccess-visualeffects                       |



### 附加信息

| “设置”页面 | URI                                                          |
| :--------- | :----------------------------------------------------------- |
| 附加信息   | ms-settings:extras（仅在通过某种方式（例如第三方）安装了“设置应用”后可用） |



### 家庭组

| “设置”页面 | URI                      |
| :--------- | :----------------------- |
| 家庭组     | ms-settings:family-group |



### 游戏

| “设置”页面 | URI                                                          |
| :--------- | :----------------------------------------------------------- |
| 游戏栏     | ms-settings:gaming-gamebar                                   |
| 游戏 DVR   | ms-settings:gaming-gamedvr                                   |
| 游戏模式   | ms-settings:gaming-gamemode                                  |
| 全屏玩游戏 | ms-settings:quietmomentsgame                                 |
| TruePlay   | ms-settings:gaming-trueplay（从 Windows 10 版本 1809（10.0，内部版本 17763）起，Windows 中会删除此功能） |



### 混合现实

只有在已安装混合现实门户应用的情况下，这些设置才可用。

| “设置”页面       | URI                                         |
| :--------------- | :------------------------------------------ |
| 音频和语音       | ms-settings:holographic-audio               |
| 环境             | ms-settings:privacy-holographic-environment |
| 头戴显示设备显示 | ms-settings:holographic-headset             |
| 卸载             | ms-settings:holographic-management          |
| 启动和桌面       | ms-settings:holographic-startupandesktop    |



### 网络和 Internet

| “设置”页面        | URI                                                          |
| :---------------- | :----------------------------------------------------------- |
| 网络和 Internet   | ms-settings:network-status                                   |
| 高级设置          | ms-settings:network-advancedsettings                         |
| 飞行模式          | ms-settings:network-airplanemode ms-settings:proximity       |
| 手机网络和 SIM 卡 | ms-settings:network-cellular                                 |
| 拨号              | ms-settings:network-dialup                                   |
| DirectAccess      | ms-settings:network-directaccess（仅在启用 DirectAccess 时才可用） |
| 以太网            | ms-settings:network-ethernet                                 |
| 管理已知网络      | ms-settings:network-wifisettings                             |
| 移动热点          | ms-settings:network-mobilehotspot                            |
| 代理              | ms-settings:network-proxy                                    |
| VPN               | ms-settings:network-vpn                                      |
| WLAN              | ms-settings:network-wifi（仅当设备具有 wifi 适配器时才可用） |
| Wi-Fi 预配        | ms-settings:wifi-provisioning                                |



### 个性化设置

| “设置”页面                     | URI                                                          |
| :----------------------------- | :----------------------------------------------------------- |
| 背景                           | ms-settings:personalization-background                       |
| 选择哪些文件夹显示于“开始”屏幕 | ms-settings:personalization-start-places                     |
| 颜色                           | ms-settings:personalization-colors ms-settings:colors        |
| 概览                           | ms-settings:personalization-glance（已在 Windows 10 版本 1809 及更高版本中弃用） |
| 锁屏界面                       | ms-settings:lockscreen                                       |
| 导航栏                         | ms-settings:personalization-navbar（已在 Windows 10 版本 1809 及更高版本中弃用） |
| 个性化（类别）                 | ms-settings:personalization                                  |
| 开始                           | ms-settings:personalization-start                            |
| 任务栏                         | ms-settings:taskbar                                          |
| 触摸键盘                       | ms-settings:personalization-touchkeyboard                    |
| 主题                           | ms-settings:themes                                           |



### 电话

| “设置”页面   | URI                                                          |
| :----------- | :----------------------------------------------------------- |
| 你的手机     | ms-settings:mobile-devices ms-settings:mobile-devices-addphone ms-settings:mobile-devices-addphone-direct（打开“你的手机”应用） |
| 设备使用情况 | ms-settings:deviceusage                                      |



### 隐私

| “设置”页面     | URI                                                          |
| :------------- | :----------------------------------------------------------- |
| 附件应用       | ms-settings:privacy-accessoryapps（已在 Windows 10 版本 1809 及更高版本中弃用） |
| 帐户信息       | ms-settings:privacy-accountinfo                              |
| 活动历史记录   | ms-settings:privacy-activityhistory                          |
| 广告 ID        | ms-settings:privacy-advertisingid（已在 Windows 10 版本 1809 及更高版本中弃用） |
| 应用诊断       | ms-settings:privacy-appdiagnostics                           |
| 自动文件下载   | ms-settings:privacy-automaticfiledownloads                   |
| 后台应用       | ms-settings:privacy-backgroundapps（已在 Windows 11 21H2 及更高版本中弃用）  **注意：**在 Windows 11 中，后台应用权限是单独访问的。 若要查看权限，请转到“应用”->“已安装的应用”，然后在新式应用中选择“...”，然后选择“高级选项”。 新式应用会显示高级页面，除非设置了组策略或设置了用户的全局切换值（Windows 10 中已弃用的设置），否则将显示“后台应用权限”部分。 若要使用 URI 访问此页面，请使用 `ms-settings:appsfeatures-app` URI 并传递应用的包系列名称的可选参数。 |
| 后台空间感知   | ms-settings:privacy-backgroundspatialperception              |
| 日历           | ms-settings:privacy-calendar                                 |
| 联络历史记录   | ms-settings:privacy-callhistory                              |
| Camera         | ms-settings:privacy-webcam                                   |
| 联系人         | ms-settings:privacy-contacts                                 |
| 文档           | ms-settings:privacy-documents                                |
| “下载”文件夹   | ms-settings:privacy-downloadsfolder                          |
| 电子邮件       | ms-settings:privacy-email                                    |
| 眼动追踪仪     | ms-settings:privacy-eyetracker（需要眼动跟踪器硬件）         |
| 反馈和诊断     | ms-settings:privacy-feedback                                 |
| 文件系统       | ms-settings:privacy-broadfilesystemaccess                    |
| 常规           | ms-settings:privacy 或 ms-settings:privacy-general           |
| 图形           | ms-settings:privacy-graphicscaptureprogrammatic ms-settings:privacy-graphicscapturewithoutborder |
| 墨迹书写和键入 | ms-settings:privacy-speechtyping                             |
| 位置           | ms-settings:privacy-location                                 |
| Messaging      | ms-settings:privacy-messaging                                |
| Microphone     | ms-settings:privacy-microphone                               |
| 动态效果       | ms-settings:privacy-motion                                   |
| 音乐库         | ms-settings:privacy-musiclibrary                             |
| 通知           | ms-settings:privacy-notifications                            |
| 其他设备       | ms-settings:privacy-customdevices                            |
| 电话联络       | ms-settings:privacy-phonecalls                               |
| 图片           | ms-settings:privacy-pictures                                 |
| 无线电收发器   | ms-settings:privacy-radios                                   |
| 语音           | ms-settings:privacy-speech                                   |
| 任务           | ms-settings:privacy-tasks                                    |
| 视频           | ms-settings:privacy-videos                                   |
| 语音激活       | ms-settings:privacy-voiceactivation                          |



### 搜索

| “设置”页面       | URI                            |
| :--------------- | :----------------------------- |
| 搜索             | ms-settings:search             |
| 搜索更多详细信息 | ms-settings:search-moredetails |
| 搜索权限         | ms-settings:search-permissions |



### Surface Hub

| “设置”页面   | URI                                     |
| :----------- | :-------------------------------------- |
| 帐户         | ms-settings:surfacehub-accounts         |
| 会话清理     | ms-settings:surfacehub-sessioncleanup   |
| 团队会议     | ms-settings:surfacehub-calling          |
| 设备管理团队 | ms-settings:surfacehub-devicemanagenent |
| 欢迎屏幕     | ms-settings:surfacehub-welcome          |



### 系统

| “设置”页面           | URI                                                          |
| :------------------- | :----------------------------------------------------------- |
| 关于                 | ms-settings:about                                            |
| 高级显示设置         | ms-settings:display-advanced（仅支持高级显示选项的设备可用） |
| 应用音量和设备首选项 | ms-settings:apps-volume（已在 Windows 10 版本 1903 中添加）  |
| 节电模式             | ms-settings:batterysaver（仅具有电池的设备可用，例如平板电脑） |
| 节电模式设置         | ms-settings:batterysaver-settings（仅具有电池的设备可用，例如平板电脑） |
| 电池使用             | ms-settings:batterysaver-usagedetails（仅具有电池的设备可用，例如平板电脑） |
| 剪贴板               | ms-settings:clipboard                                        |
| 显示                 | ms-settings:display                                          |
| 默认保存位置         | ms-settings:savelocations                                    |
| 显示                 | ms-settings:screenrotation                                   |
| 复制我的显示器       | ms-settings:quietmomentspresentation                         |
| 在这些小时内         | ms-settings:quietmomentsscheduled                            |
| 加密                 | ms-settings:deviceencryption                                 |
| 能源推荐             | ms-settings:energyrecommendations（已在适用于 Windows 11 版本 22H2 内部版本 22624 的 2 月 Moment 更新中添加） |
| 专注助手             | ms-settings:quiethours                                       |
| 图形设置             | ms-settings:display-advancedgraphics（仅支持高级图形选项的设备可用） |
| 图形默认设置         | ms-settings:display-advancedgraphics-default                 |
| 多任务               | ms-settings:multitasking ms-settings:multitasking-sgupdate   |
| 夜灯设置             | ms-settings:nightlight                                       |
| 投影到此电脑         | ms-settings:project                                          |
| 共享体验             | ms-settings:crossdevice                                      |
| 平板模式             | ms-settings:tabletmode（已在 Windows 11 中删除）             |
| 任务栏               | ms-settings:taskbar                                          |
| 通知和操作           | ms-settings:notifications                                    |
| 远程桌面             | ms-settings:remotedesktop                                    |
| 电话                 | ms-settings:phone（已在 Windows 10 版本 1809 及更高版本中弃用） |
| 电源和睡眠           | ms-settings:powersleep                                       |
| 存在感测             | ms-settings:presence（已在适用于 Windows 11 版本 22H2 内部版本 22624 的 5 月 Moment 更新中添加） |
| 声音                 | ms-settings:sound                                            |
| 声音设备             | ms-settings:sound-devices                                    |
| 存储                 | ms-settings:storagesense                                     |
| 存储感知             | ms-settings:storagepolicies                                  |
| 有关存储的建议       | ms-settings:storagerecommendations                           |
| 磁盘和卷             | ms-settings:disksandvolumes                                  |



### 时间和语言

| “设置”页面    | URI                                                          |
| :------------ | :----------------------------------------------------------- |
| 日期和时间    | ms-settings:dateandtime                                      |
| 日本 IME 设置 | ms-settings:regionlanguage-jpnime（安装了 Microsoft Japan 输入法编辑器则可用） |
| 区域          | ms-settings:regionformatting                                 |
| 语言          | ms-settings:keyboard ms-settings:keyboard-advanced ms-settings:regionlanguage ms-settings:regionlanguage-bpmfime ms-settings:regionlanguage-cangjieime ms-settings:regionlanguage-chsime-wubi-udp ms-settings:regionlanguage-quickime ms-settings:regionlanguage-korime |
| 拼音 IME 设置 | ms-settings:regionlanguage-chsime-pinyin（在安装了 Microsoft 拼音输入法编辑器的情况下可用） ms-settings:regionlanguage-chsime-pinyin-domainlexicon ms-settings:regionlanguage-chsime-pinyin-keyconfig ms-settings:regionlanguage-chsime-pinyin-udp |
| 语音          | ms-settings:speech                                           |
| 五笔 IME 设置 | ms-settings:regionlanguage-chsime-wubi（安装了 Microsoft 五笔输入法编辑器则可用） |
| 添加显示语言  | ms-settings:regionlanguage-adddisplaylanguage                |
| 语言选项      | ms-settings:regionlanguage-languageoptions                   |
| 设置显示语言  | ms-settings:regionlanguage-setdisplaylanguage                |



### 更新和安全

| “设置”页面                    | URI                                                          |
| :---------------------------- | :----------------------------------------------------------- |
| 激活                          | ms-settings:activation                                       |
| 备份                          | ms-settings:backup（已在 Windows 11 中删除的页；打开同步）   |
| 传递优化                      | ms-settings:delivery-optimization ms-settings:delivery-optimization-activity ms-settings:delivery-optimization-advanced |
| 查找我的设备                  | ms-settings:findmydevice                                     |
| 面向开发人员                  | ms-settings:developers                                       |
| 恢复                          | ms-settings:recovery                                         |
| 启动安全密钥注册              | ms-settings:signinoptions-launchsecuritykeyenrollment        |
| 疑难解答                      | ms-settings:troubleshoot                                     |
| Windows 安全性                | ms-settings:windowsdefender                                  |
| Windows 预览体验计划          | ms-settings:windowsinsider（仅当用户在 WIP 中注册后才存在） ms-settings:windowsinsider-optin |
| Windows 更新                  | ms-settings:windowsupdate ms-settings:windowsupdate-action   |
| Windows 更新-活动小时数       | ms-settings:windowsupdate-activehours                        |
| Windows 更新-高级选项         | ms-settings:windowsupdate-options                            |
| Windows 更新-可选更新         | ms-settings:windowsupdate-optionalupdates                    |
| Windows 更新-重启选项         | ms-settings:windowsupdate-restartoptions                     |
| Windows 更新-按需查找器       | ms-settings:windowsupdate-seekerondemand                     |
| Windows 更新-查看更新历史记录 | ms-settings:windowsupdate-history                            |



### 用户帐户

| “设置”页面       | URI                                                          |
| :--------------- | :----------------------------------------------------------- |
| 预配             | ms-settings:workplace-provisioning（仅当企业部署了预配程序包时才可用） |
| 修复令牌         | ms-settings:workplace-repairtoken                            |
| 预配             | ms-settings:provisioning（仅当移动设备上企业部署了预配程序包时才可用） |
| Windows Anywhere | ms-settings:windowsanywhere（设备必须支持 Windows Anywhere） |

(完)

**参考**

- [启动 Windows 设置应用](https://learn.microsoft.com/zh-cn/windows/uwp/launch-resume/launch-settings-app)