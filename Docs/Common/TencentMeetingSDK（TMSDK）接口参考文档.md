
# 文档修订记录
| 日期 | 修改内容 | SDK版本 |
|---|---|---|
| 2021-7-18 | 第一版初稿 | 2.18.0 |
| 2021-8-6 | 对错误码做了统一描述 | 2.18.0 |
| 2021-8-18 | 新增接口：设置会议信息和邀请信息回调接口 | 2.18.0 |
| 2021-8-26 | 接口调整：登录时如果账号已登录是否强制让对方下线 | 2.18.0 |
| 2021-8-30 | 接口调整：入会和离会回调接口增加meeting_code字段 | 2.18.0 |
| 2021-10-9 | 新增接口：获取登录态URL、显示历史会议列表、显示历史会议详情页 | 2.18.1 |
| 即将推出 | 新增的接口：快速创建会议、预定会议、获取当前会议信息、显示加入会议界面、显示设置界面 | -- |



# 1. SDK使用说明

以下方法为伪代码，起示意作用，具体各终端代码样例参考SDK包中的Demo代码工程。

## 1.1 获取SDK实例方法
``` 
tm_sdk = TMSDK.getInstance()
```

## 1.2 获取Service方法
```
account_service = tm_sdk.getAccountService()        //获取AccountService
pre_meeting_service = tm_sdk.getPreMeetingService() //获取PreMeetingService
in_meeting_service = tm_sdk.getInMeetingService()   //获取InMeetingService
```

## 1.3 SDK 调用的基本时序
1. 获取SDK实例
2. SDK初始化
    1. 调用`TMSDK.initialize`进行SDK初始化，并在参数中设置回调代理`SDKCallback`
    2. 响应SDK初始化回调`SDKCallback.onSDKInitializeResult`，**回调结果成功才表示初始化完成**
3. 登录
    1. 获取`AccountService`实例
    2. 设置回调代理`setAuthenticationCallback`
    3. 调用`AccountService.login`进行登录
    4. 响应登录回调`AuthenticationCallback.onLogin`，**回调结果成功表示登录成功**
4. 入会
    1. 获取`PreMeetingService`实例
    2. 设置回调代理`setPreMeetingCallback`         
    3. 调用`PreMeetingService.joinMeeting`进行入会
    4. 响应入会回调`PreMeetingCallback.onJoinMeeting`，回调结果成功表示入会成功


# 2. TMSDK

## 2.1 InitParam数据结构

|产品 |属性 |类型 |必填 |默认值 |说明 |
|---|---|---|---|---|---|
|公有云SDK专用 |sdk_id |string |必填 |(无) |SDK ID |
|公有云SDK专用|sdk_token |string |必填 |(无) |SDK Token |
|私有化SDK专用 |server_host |string |必填，二选一 |(无) |私有化服务器地址，格式为：{protocol}://{domain}:{port}，protocol默认为http；port默认为29666 |
|私有化SDK专用|org_domain |string |必填，二选一 |(无) |组织机构域，如填写，SDK则会通过`org_domain`从公有云服务上获取私有化服务器地址，并覆盖`server_host`的值 |
|通用 |data_path |string |否 | %AppData%\Tencent\WeMeet\Global\Logs | 仅`Windows`支持：自定义SDK数据存储路径，里面包括日志目录。 |
|通用 |app_name |string |否 |腾讯会议 | 指定显示的品牌名称 |


## 2.2 SDKCallback回调代理

### onSDKInitializeResult
* 说明：调用SDK初始化的结果回调

|参数名 |参数类型 | 参数说明 |
|---|---|---|
| code | int | SDK初始化结果码 |
| msg | string | SDK初始化结果信息 |

### onShowLogsResult【即将推出】
* 说明：调用`showLogs`的结果回调
* 可用版本：>= 2.18.2

|参数名 |参数类型 | 参数说明 |
|---|---|---|
| code | int | 打开日志文件夹结果码 |
| msg | string | 打开日志文件夹结果信息 |

### onSDKError 
|参数名 |参数类型 | 参数说明 |
|---|---|---|
| code | int | 错误码 |
| msg | string | 错误信息 |


## 2.3 TMSDK成员

### getSDKVersion
* 函数形式：string getSDKVersion()
* 函数说明：获取当前SDK版本号。
* 返回值类型：string
* 返回值说明：版本号信息
* 参数说明：无

### initialize
* 函数形式：void initialize(InitParam init_param, SDKCallback callback)
* 函数说明：初始化SDK并设置回调代理，除`getSDKVersion`之外，在调用的所有接口函数之前，`必须第一个调用该函数`。初始化成功后，重复调用无效。通过`onSDKInitializeResult`回调来返回初始化结果。
* 返回值类型：void
* 返回值说明：无
* 参数说明：

|参数名 |参数类型 |参数必填 |参数默认值 |参数说明 |
|---|---|---|---|---|
|init_param |**InitParam** |是 |(无) |初始化参数 |
|callback |**SDKCallback** |是 |(无) |SDK回调代理，接入方实现该代理，用来响应SDK回调 |

### isInitialized
* 函数形式：bool isInitialized()
* 函数说明：判断是否已初始化SDK成功。
* 返回值类型：bool
* 返回值说明：是否已经初始化SDK
* 参数说明：无

### refreshSDKToken
* 函数形式：int refreshSDKToken(string new_sdk_token)
* 函数说明：更新SDK Token，替换掉过期或快过期的SDK Token。
* 返回值类型：int
* 返回值说明：处理结果的错误码，0表示成功；其他值表示失败，详情参考`6. 错误码`章节。
* 参数说明：

|参数名 |参数类型 |参数必填 |参数默认值 |参数说明 |
|---|---|---|---|---|
|new_sdk_token |string |是 |(无) |新SDK Token |

### getCurrentSDKToken
* 函数形式：string getCurrentSDKToken()
* 函数说明：获取当前SDK Token的值。
* 返回值类型：string
* 返回值说明：当前的SDK Token值
* 参数说明：无

### showLogs
* 函数形式：void showLogs()
* 函数说明：帮助用户获取日志，移动端会对日志目录打包，并打开系统的分享；桌面端会打开日志文件夹。
* 返回值类型：void
* 返回值说明：无
* 参数说明：无
* 各终端平台下的日志路径：

| 终端 | 日志路径 |
| ------ | ------ |
| Windows | %AppData%\Tencent\WeMeet\Global\Logs 或者是接入方设置的自定义数据目录下 |
| MacOS | ~/Library/Containers/{宿主App的BundleID}/Data/Library/Global/Logs |
| iOS | {宿主App沙盒路径}/AppData/Library/Application Support/{宿主App的BundleID}/Global/Logs |
| Android | /Data/Data/{宿主App的PackageName}/Global/Logs |


### getAccountService
* 函数形式：string getAccountService()
* 函数说明：获取SDK`AccountService`的对象实例。
* 返回值类型：AccountService
* 返回值说明：`AccountService`的对象实例
* 参数说明：无

### getPreMeetingService
* 函数形式：string getPreMeetingService()
* 函数说明：获取SDK`PreMeetingService`的对象实例。
* 返回值类型：PreMeetingService
* 返回值说明：`PreMeetingService`的对象实例
* 参数说明：无

### getInMeetingService
* 函数形式：string getInMeetingService()
* 函数说明：获取SDK`InMeetingService`的对象实例。
* 返回值类型：InMeetingService
* 返回值说明：`InMeetingService`的对象实例
* 参数说明：无


# 3. AccountService说明
AccountService用来管理账户的登录、登出和账户信息，在所有会议的操作之前，都必须先登录成功。

## 3.1 AuthenticationCallback回调代理

### onLogin
* 说明：账户登录的回调。

|参数名 |参数类型 |参数说明 |
|---|---|---|
| code | int | 结果码：0表示成功；其他值表示失败，详情参考`6. 错误码`章节 |
|msg |string |结果信息 |

### onLogout
* 说明：账户登出的回调。

|参数名 |参数类型 |参数说明 |
|---|---|---|
|type |int |登出类型：1、手动登出；2、强制登出（同端登录被踢） |
|code |int |结果码：0表示成功；其他值表示失败，详情参考`6. 错误码`章节|
|msg |string |结果信息 |


## 3.2 AccountService成员

### setCallback
* 函数形式：void setCallback(AuthenticationCallback callback)
* 函数说明：设置回调代理`AuthenticationCallback`，重复调用会覆盖原有回调代理的值。
* 返回值类型：void
* 返回值说明：无
* 参数说明：

|参数名 |参数类型 |参数必填 |参数默认值 |参数说明 |
|---|---|---|---|---|
|callback |AuthenticationCallback |是 |(无) |接入方实现的回调代理实例 |

### login
* 函数形式：void login(string sso_url)
* 函数说明：发起登录请求，登录结果会在回调`AuthenticationCallback.onLogin`返回
* 返回值类型：void
* 返回值说明：无
* 参数说明：

|参数名 |参数类型 |参数必填 |参数默认值 |参数说明 |
|---|---|---|---|---|
|sso_url |string |是 |(无) |单点登录的URL地址，由接入方的服务端生成并返回给接入方客户端 |

### logout
* 函数形式：void logout()
* 函数说明：发起登出请求，登录结果会在回调`AuthenticationCallback.onLogout`返回
* 返回值类型：void
* 返回值说明：无
* 参数说明：无

### isLoggedIn
* 函数形式：void isLoggedIn()
* 函数说明：判断是否已登录
* 返回值类型：bool
* 返回值说明：是否已登录
* 参数说明：无

### jumpUrlWithLoginStatus
* 函数形式：void jumpUrlWithLoginStatus(string target_url)
* 函数说明：带登录态去打开目标地址，该地址必须是会议相关的、并支持登录态方式的页面，必须登录成功才可调用。
* 返回值类型：void
* 返回值说明：无
* 参数说明：

|参数名 |参数类型 |参数必填 |参数默认值 |参数说明 |
|---|---|---|---|---|
|taget_url | string | 是 | (无) | 要跳转的目标URL地址，该地址对应页面必须是会议相关的地址，比如云录制页 |

### getUrlWithLoginStatus
* 可用版本：>= 2.18.1
* 函数形式：string getUrlWithLoginStatus(string target_url)
* 函数说明：获取一个带登录态的URL链接，该地址必须是会议相关的、并支持登录态方式的页面，必须登录成功才可调用。
* 返回值类型：string
* 返回值说明：一个带登录态的URL链接
* 参数说明：

|参数名 |参数类型 |参数必填 |参数默认值 |参数说明 |
|---|---|---|---|---|
|taget_url | string | 是 | (无) | 要访问的目标URL地址，该地址对应页面必须是会议相关的地址，比如云录制页 |


# 4. PreMeetingService说明
用来管理会议前相关的操作和数据的获取，包括入会、快速会议、预定会议、投屏、获取会议列表等等

## 4.1 PreMeetingCallback回调代理

### onJoinMeeting
* 说明：入会的回调。

|参数名 |参数类型 |参数说明 |
|---|---|---|
|code |int |结果码：0表示成功；其他值表示失败，详情参考`6. 错误码`章节 |
|msg |string |结果信息 |
| meeting_code | string | 会议号 |

### onShowScreenCastViewResult【即将移除】
* 说明：打开无线投屏的回调。

|参数名 |参数类型 |参数说明 |
|---|---|---|
|code |int |结果码：0表示成功；其他值表示失败，详情参考`6. 错误码`章节|
|msg |string |结果信息 |

### onActionResult【即将推出】
* 说明：会前界面各种用户行为操作的回调。
* 可用版本：>= 2.18.2

|参数名 |参数类型 |参数说明 |
|---|---|---|
|action_type |int |表示用户的何种行为操作，详情参考下表 |
|code |int |结果码：0表示成功；其他值表示失败，详情参考`6. 错误码`章节|
|msg |string |结果信息 |

其中`action_type`值对应的含义如下：

| 名称 | 行为操作的枚举值 | 说明 |
|---|---|---|
| ShowPreMeetingView | 0    | 打开会前界面的回调 |
| ShowScreenCastView | 1    | 打开无线投屏的回调 |
| ShowHistoricalMeetingView | 2    | 打开历史会议界面的回调|
| ShowMeetingDetailView | 3    | 打开某一会议详情的回调 |
| ShowJoinMeetingView | 4    | 打开加入会议界面的回调 |
| ShowScheduleMeetingView | 5    | 打开预定会议界面的回调 |
| ShowMeetingSettingView | 6    | 打开会议设置界面的回调 |

## 4.2 PreMeetingService成员

### setCallback
* 函数形式：void setCallback(PreMeetingCallback callback)
* 函数说明：设置回调代理`PreMeetingCallback`，重复调用会覆盖原有回调代理的值。
* 返回值类型：void
* 返回值说明：无
* 参数说明：

|参数名 |参数类型 |参数必填 |参数默认值 |参数说明 |
|---|---|---|---|---|
|callback |PreMeetingCallback |是 |(无) |接入方实现的回调代理实例 |

### joinMeeting
* 函数形式：void joinMeeting(JoinParam param)
* 函数说明：发起入会请求，结果会在回调`PreMeetingCallback.onJoinMeeting`返回。登录完成后，才可调用。
* 返回值类型：void
* 返回值说明：无
* 参数说明：

|参数名 |参数类型 |参数必填 |参数默认值 |参数说明 |
|---|---|---|---|---|
|param |**JoinParam** |是 |(无) |入会参数 |
* JoinParam格式

|属性 |类型 |必填 |默认值 |说明 |
|---|---|---|---|---|
|meeting_code |string |是 |(无) |会议号 |
|user_display_name |string |否 |账户的用户名 |会议中显示的名称 |
|password |string |否 |(空) |会议密码 |
|invite_url |string |否 |腾讯会议默认URL链接 |自定义URL链接 |
|mic_on |bool |否 |SDK默认设置 |是否开启麦克风 |
|camera_on |bool |否 |SDK默认设置 |是否开启摄像头 |
|speaker_on |bool |否 |SDK默认设置 |是否开启扬声器(仅移动端) |
|face_beauty_on |bool |否 |SDK默认设置 |是否开启美颜 |

### showPreMeetingView
* 函数形式：void showPreMeetingView()
* 函数说明：显示SDK自带的会前界面。登录完成后，才可调用。
* 返回值类型：void
* 返回值说明：无
* 参数说明：无

### showScreenCastView
* 函数形式：void showScreenCastView()
* 函数说明：显示SDK自带的投屏码输入界面。登录完成后，才可调用。
* 返回值类型：void
* 返回值说明：无
* 参数说明：无

### showHistoricalMeetingView
* 可用版本：>= 2.18.1
* 函数形式：void showHistoricalMeetingView()
* 函数说明：显示用户历史会议界面。登录完成后，才可调用。
* 返回值类型：void
* 返回值说明：无
* 参数说明：无

### showMeetingDetailView
* 可用版本：>= 2.18.1
* 函数形式：void showMeetingDetailView(string meeting_id, string current_sub_meeting_id)
* 函数说明：显示某一个具体会议的界面。登陆完成后，才可调用。如果输入错误的meeting_id或者current_sub_meeting_id有的字段会显示’-‘
* 返回值类型：void
* 返回值说明：无
* 参数说明：

|参数名 |参数类型 |参数必填 |参数默认值 |参数说明 |
|---|---|---|---|---|
|meeting_id |string |是 |(无) |会议标识号 |
|current_sub_meeting_id |string |是|(无)|非周期性会议时值为0；周期会议时，可以通过腾讯会议“查询用户的会议列表”的REST APis获取 |

### showJoinMeetingView【即将推出】
* 可用版本：>= 2.18.2
* 函数形式：void showJoinMeetingView()
* 函数说明：显示加入会议界面。登录完成后，才可调用。
* 返回值类型：void
* 返回值说明：无
* 参数说明：无

### showScheduleMeetingView【即将推出】
* 可用版本：>= 2.18.2
* 函数形式：void showScheduleMeetingView(string meeting_type)
* 函数说明：显示预定会议会议界面。登录完成后，才可调用。
* 返回值类型：void
* 返回值说明：无
* 参数说明：

|参数名 |参数类型 |参数必填 |参数默认值 |参数说明 |
|---|---|---|---|---|
|meeting_type | string | 否 | 0 | 会议类型，0:普通会议；1:在线大会 |

### showMeetingSettingView【即将推出】
* 可用版本：>= 2.18.2
* 函数形式：void showMeetingSettingView()
* 函数说明：显示设置管理界面。登录完成后，才可调用。
* 返回值类型：void
* 返回值说明：无
* 参数说明：无


# 5. InMeetingService说明
用来管理会议中的操作和界面的控制。

## 5.1 InMeetingCallback回调代理

### onLeaveMeeting
* 说明：离会的回调。
* 返回值：无
* 参数说明：

|参数名 |参数类型 |参数说明 |
|---|---|---|
|type |int |离会类型，1：用户自身操作离会；2：被踢出会议；3：会议结束 |
|code |int |结果码：0表示成功；其他值表示失败，详情参考`6. 错误码`章节 |
|msg |string |结果信息|
| meeting_code | string | 会议号 |

### onInviteMeeting
* 说明：用户在会议中界面点击下方工具栏邀请按钮后的回调。
* 返回值：无
* 参数说明：

|参数名 |参数类型 |参数说明 |
|---|---|---|
|invite_info |string |邀请的相关信息，JSON字符串 |

invite_info内容
```
{
    "begin_time": 1629194320, // 会议开始时间戳
    "end_time": 1629197920, // 会议结束时间戳
    "create_type": 1, // 会议类型，1 普通会议，2 快速会议
    "device_dial_in_guide": "", // 设备接入指南信息
    "general_content": "", // 邀请入会的通用信息
    "host_name": "", // 主持人昵称
    "meeting_code": "", // 会议号
    "meeting_url": "", // 会议链接
    "mra": "",
    "mra_ics": "",
    "password": "", // 会议密码
    "pstn": "",
    "meeting_pstn_json": "",
    "subject": "",// 会议主题
    "participate_id": "" //参会者id
}
```
### onShowMeetingInfo
* 说明：用户在会议中界面点击展示会议信息的回调。
* 返回值：无
* 参数说明：

|参数名 |参数类型 |参数说明 |
|---|---|---|
|meeting_info |string |会议信息，JSON字符串，meeting_info目前跟invite_info内容一样 |


## 5.2 InMeetingService成员

### setCallback
* 函数形式：void setCallback(InMeetingCallback callback)
* 函数说明：设置回调代理`InMeetingCallback`，重复调用会覆盖原有回调代理的值。
* 返回值类型：void
* 返回值说明：无
* 参数说明：

|参数名 |参数类型 |参数必填 |参数默认值 |参数说明 |
|---|---|---|---|---|
|callback |InMeetingCallback |是 |(无) |接入方实现的回调代理实例 |

### leaveMeeting
* 函数形式：void leaveMeeting(bool end_meeting)
* 函数说明：发起离会请求，结果会在回调`InMeetingCallback.onLeaveMeeting`返回。
* 返回值类型：void
* 返回值说明：无
* 参数说明：

|参数名 |参数类型 |参数必填 |参数默认值 |参数说明 |
|---|---|---|---|---|
|end_meeting |bool |否 |false |是否结束会议，仅当前账户是会议主持人时，该参数才有效 |

### enableInviteCallback
* 函数形式：void enableInviteCallback(bool enable, bool show)
* 函数说明：设置是否使用邀请回调，如果使用，点击会议中界面下方工具栏上的邀请按钮，会将会议信息通过onInviteMeeting回调
* 返回值类型：void
* 返回值说明：无
* 参数说明：

|参数名 |参数类型 |参数必填 |参数默认值 |参数说明 |
|---|---|---|---|---|
|enable |bool |否 |false |是否使用 |
|show   |bool |是 |true  |是否显示邀请页面，如果为true，SDK不会展示自身邀请界面，完全由接入方实现邀请界面和内容展示；如果为false，则还是显示SDK的邀请界面。<br>而如果enable为false，则show在SDK中被强制设置为true。|

### enableMeetingInfoCallback
* 函数形式：void enableMeetingInfoCallback(bool enable, bool show)
* 函数说明：设置是否使用会议信息回调，如果使用，点击会议title后面(i)信息按钮，会将会议信息通过onShowMeetingInfo回调
* 返回值类型：void
* 返回值说明：无
* 参数说明：

|参数名 |参数类型 |参数必填 |参数默认值 |参数说明 |
|---|---|---|---|---|
|enable |bool |否 |false |是否使用 |
|show   |bool |是 |true  |是否显示会议信息页面，如果为true，SDK不会展示自身会议信息界面，完全由接入方实现会议信息界面和内容展示；如果为false，则还是显示SDK的会议信息界面。<br>而如果enable为false，则show在SDK中被强制设置为true。|


# 6. 错误码

| 名称 | 错误码 | 说明 |
|---|---|---|
| kTMSDKErrorSuccess | 0                 | 成功。 |
| kTMSDKErrorServerConfigFail | -1001    | 设置服务地址或获取服务配置失败 |
| kTMSDKErrorInvalidAuthCode | -1002     | 获取AuthCode，登录时传入参数不正确可能会导致 |
| kTMSDKErrorLogoutInMeeting | -1003     | 正在会议中，无法退出，需先离会 |
| kTMSDKErrorLoginAborted | -1004        | 多次调用Login时，前次登录过程取消 |
| kTMSDKErrorUnknown | -1005             | 未知错误，出现该错误码，请官方联系 |
| kTMSDKErrorUserNotAuthorized | -1006   | 未登录。在入会、投屏、显示会前界面之前没有成功登录。 |
| kTMSDKErrorUserInMeeting | -1007       | 已在会议中。在入会、投屏、显示会前界面的时候，用户在会议中，需先退出。 |
| kTMSDKErrorInvalidParam | -1008        | 无效参数。在调用SDK接口时，包含无效参数。 |
| kTMSDKErrorInvalidMeetingCode | -1009  | 无效会议号 |
| kTMSDKErrorInvalidNickname | -1010     | 无效入会的用户名称，可能长度过长导致 |
| kTMSDKErrorDuplicateInitCall | -1011   | 重复调用初始化 |
| kTMSDKErrorAccountAlreadyLogin | -1012  | 账号已登录|
| kTMSDKErrorSdkNotInitialized | -1013  | SDK未初始化|
| kTMSDKErrorSyncCallTimeout | -1014  | SDK同步调用超时|
| kTMSDKErrorNotInMeeting | -1015  | 非入会状态调用会议中接口|
| kTMSDKErrorCancelJoin | -1016  | 用户手动取消入会 |
| kTMSDKErrorIsLogining | -1017  | 已经在登录状态中，重复登录 |
| kTMSDKErrorLoginNetError | -1018  | 登陆过程出现网络错误 |
| kTMSDKErrorTokenVerifyFailed | -1019  | token校验失败，可能是token过期或token失效，需要refreshToken后再登录 |
