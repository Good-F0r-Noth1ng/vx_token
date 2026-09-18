# vx_token

一个仅在本机运行的 Windows 命令行工具，用于从本人已经登录的“龙猫体育锻炼”微信小程序进程中读取登录 Token。

> Token 属于敏感登录凭证。请仅处理本人账号和本人有权访问的环境，不要公开、提交或转发真实 Token。

## 工作原理

微信小程序由 `WeChatAppEx.exe` 进程承载。登录后，相关 Token 可能以明文形式存在于该进程内存中。本工具使用 Windows 的 `OpenProcess`、`VirtualQueryEx` 和 `ReadProcessMemory` API 进行只读扫描：

1. 优先查找窗口标题包含“龙猫”的 `WeChatAppEx.exe` 进程。
2. 扫描候选进程中已提交且可读取的内存区域。
3. 匹配以 `WXXCX` 开头且长度不小于 80 个字符的字符串。
4. 将找到的 Token 显示在终端，并尝试复制到 Windows 剪贴板。

程序不写入目标进程、不注入代码，源码中没有网络上传逻辑。

## 配合 Totoro 使用

本工具可用于获取 [Totoro](https://github.com/yuyuyudlc/Totoro) 登录页面所需的微信小程序 Token：

1. 在微信桌面客户端中打开并登录“龙猫体育锻炼”小程序。
2. 运行本工具并获取 Token。
3. 将 Token 粘贴到 Totoro 的 Token 登录输入框中。Totoro 支持直接输入 Token，也支持带 `Bearer ` 前缀的形式。

本项目是独立的第三方辅助工具，不属于 Totoro 项目，也不代表其作者提供、认可或维护本工具。Totoro 的接口、Token 格式或登录流程发生变化后，本工具可能失效；兼容性以 Totoro 上游项目的当前说明为准。

## 安全与授权

- 仅处理本人账号，或已获得账号所有者明确授权的环境。
- Token 等同于登录凭证。不要将真实 Token 提交到 GitHub、Issue、日志、截图或聊天记录中。
- Token 会显示在终端并尝试写入系统剪贴板。使用后请关闭终端，并按需清除剪贴板和剪贴板历史。
- 本工具不校验 Token，也不会主动向 Totoro 或小程序服务器发送 Token。
- 使用者应自行确认其使用方式符合所在学校、服务提供方和相关平台的规则。

## 环境要求

- Windows 10/11
- 微信桌面客户端
- 已打开并登录“龙猫体育锻炼”微信小程序
- 运行源码时需要 Python 3.7 或更高版本

## 运行源码

双击 `run.bat`，或者在项目目录中执行：

```powershell
python memory_scanner.py
```

`run.bat` 会自动切换到脚本所在目录，因此整个项目文件夹移动或改名后仍可使用。

## 构建 Windows EXE

```powershell
python -m pip install pyinstaller
python -m PyInstaller --noconfirm --onefile --name open-totoro-token memory_scanner.py
```

生成文件：

```text
dist\open-totoro-token.exe
```

建议将 EXE 作为 GitHub Release 附件发布，不要提交到源码历史。由于程序会读取其他进程内存，安全软件可能产生警告；发布时应同时提供源码和 SHA-256 校验值。

## 已知限制

- 微信可能启动多个 `WeChatAppEx.exe` 进程。标题匹配失败时，程序会继续扫描其他同名进程，因此可能找到非目标小程序中的候选字符串。
- 当前程序返回第一个符合格式的候选 Token，使用前应确认它属于目标小程序。
- 静态源码检查或成功构建只能证明程序可解析、可启动，不能替代真实登录环境中的功能验证。

## 许可证

本项目采用 [MIT License](LICENSE)。许可证仅授予软件版权相关权利，不授予访问第三方账号、系统或服务的权限。

## 文件说明

```text
memory_scanner.py  核心源码
run.bat            源码启动脚本
.gitignore         Git 忽略规则
LICENSE            MIT 许可证与版权信息
README.md          项目说明
```
