---
name: winapp-mcp
description: 让AI帮你操作Windows电脑——55个UIA工具，截图、点击、填表、读元素，像人一样操控桌面应用
version: 1.0.0
author: fengloulai
tags:
  - windows
  - automation
  - mcp
  - uia
  - desktop
  - ai-agent
---

# WinApp MCP

> 装上这个，你的AI助手就能像真人一样操作你的Windows电脑了。

## 这是什么？

简单说：**让你的AI能"动手"，不光"动嘴"。**

平时你用Claude Code、Hermes Agent这些AI助手，它只能写代码、聊天。装了WinApp MCP之后，它能直接操控你电脑上的软件：

- 🖱️ 帮你自动填网页表单、Excel表格
- 📸 帮你截图分析屏幕内容
- ⌨️ 帮你按快捷键、点按钮
- 🔍 帮你找到窗口里的文字和按钮

## 谁需要这个？

- 你用AI助手，但发现它**只能说话不能动手**——你需要这个
- 你有重复的电脑操作想让AI代劳（填表、截图、点击）——你需要这个
- 你在Windows上办公，想让AI真正"干活"而不是"建议"——你需要这个

## 安装（手把手，踩过的坑都标出来了）

### 第一步：确认Python装好了没

打开 **PowerShell**（Win+R → 输入 `powershell` → 回车）：

```bash
python --version
```

**必须显示 Python 3.10 以上。**

> ⚠️ **90%的人卡在这一步**：如果显示"python 不是内部命令"，去 [python.org](https://www.python.org) 下载安装包，**安装时一定要勾选底部的"Add Python to PATH"**。已经装了但没勾的，重装一遍选"Modify"勾上就行。

### 第二步：下载代码

```bash
git clone https://github.com/fengloulai/winapp-mcpWindowsUIAutomationMCPServer--remotecontrolWindowsdesktopappsvia.git
cd winapp-mcpWindowsUIAutomationMCPServer--remotecontrolWindowsdesktopappsvia
```

> 没装git？去 [git-scm.com](https://git-scm.com) 下载，一路下一步就行。或者直接点GitHub页面上的"Code → Download ZIP"解压也行。

### 第三步：装依赖（最容易报错的一步）

```bash
pip install -r requirements.txt
```

**踩坑记录**：

| 报错 | 原因 | 解决 |
|------|------|------|
| `pip 不是内部命令` | Python没勾选PATH | 重装Python勾选"Add Python to PATH" |
| `error: Microsoft Visual C++ 14.0 is required` | 缺少C++编译工具 | 下载 [Visual C++ Build Tools](https://visualstudio.microsoft.com/visual-cpp-build-tools/)，安装时勾选"桌面C++开发" |
| 红色权限报错 | 没用管理员运行 | 右键PowerShell → 以管理员身份运行 |
| `No module named xxx` | 依赖没装全 | 再跑一次 `pip install -r requirements.txt` |
| 下载慢/超时 | 国内网络问题 | 加国内镜像：`pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple` |

### 第四步：测试

```bash
python -m winapp_mcp --stdio
```

看到启动信息（类似 `started server`）就说明成功了。**Ctrl+C 退出**。

## 配置你的AI助手（复制粘贴就行）

### 配 Hermes Agent

在 Hermes 的 `config.yaml` 里找到 `mcp_servers`，加上：

```yaml
mcp_servers:
  winapp:
    command: python
    args: ["-m", "winapp_mcp", "--stdio"]
```

重启 Hermes，用 `/mcp` 命令检查是否加载成功。

> ⚠️ **注意**：如果 `python` 指令不行，改成 Python 的完整路径（比如 `C:\Users\你的用户名\AppData\Local\Programs\Python\Python310\python.exe`）。

### 配 Claude Desktop

在 `claude_desktop_config.json` 里加：

```json
{
  "mcpServers": {
    "winapp": {
      "command": "python",
      "args": ["-m", "winapp_mcp", "--stdio"]
    }
  }
}
```

> ⚠️ **很多新手在这卡住**：JSON 最后一项末尾不能有逗号！配完用 [jsonlint.com](https://jsonlint.com) 验证一下格式。

## 55个工具一览

| 类别 | 数量 | 能干什么 |
|------|------|---------|
| 📸 截图 | 5个 | 窗口截图、标注截图、截图对比 |
| 🖱️ 鼠标 | 6个 | 单击、双击、右键、拖拽、坐标点击 |
| ⌨️ 键盘 | 2个 | 单键、组合键（Ctrl+C等） |
| 📝 填表 | 2个 | 单个填、批量填 |
| 🔍 找元素 | 8个 | 精准查找、模糊匹配、属性查找 |
| 🪟 窗口 | 6个 | 列出窗口、附加进程、恢复最小化 |
| 📖 读取 | 5个 | 读元素、读快照、读剪切板 |
| ⏳ 等待 | 3个 | 等待元素出现、等待条件、等待空闲 |
| 🔧 其他 | 18个 | 滚动、展开折叠、事件监控等 |

## 常见问题

**Q: 双击exe报错"不是有效的Win32程序"？**
A: 这是Python项目不是exe，用 `python -m winapp_mcp --stdio` 启动。

**Q: 截图是黑屏？**
A: 目标窗口可能最小化了。先让AI调用 `restore_window` 恢复窗口再截。

**Q: 找元素找不到？**
A: ①先 `list_desktop_windows` 确认窗口在桌面上；②有些程序（如老旧软件）UIA支持差，用 `click_at_coordinates` 坐标点击代替。

**Q: WSL里能用吗？**
A: **不能**。这是Windows原生工具，必须在Windows的PowerShell或CMD里跑。但你可以从WSL远程调用它。

**Q: 操控浏览器时点了没反应？**
A: 现代网页框架（React/Ant Design）用 `set_element_value` 设值不触发事件。改用 `click_element` 点输入框 → `type_text` 输入 → 这样才触发onChange。

---

有问题去 [GitHub Issues](https://github.com/fengloulai/winapp-mcpWindowsUIAutomationMCPServer--remotecontrolWindowsdesktopappsvia/issues) 提，看到会回。
