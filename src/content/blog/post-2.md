---
title: 'Install PSReadLine in Windows Terminal, which has PowerShell already installed.'
description: '在 Windows Terminal 的 PowerShell 中安装配置 PSReadLine 很简单'
pubDate: 2026-04-09
tags: ["astro", "blogging", "learning in public"]
---


在 Windows Terminal 的 PowerShell 中安装配置 PSReadLine 很简单，只需几步就能获得命令行预测、语法高亮等强大功能。

### ⚙️ 安装与基础配置

整个流程分为三步：安装模块、启用预测、保存配置。

#### 第一步：安装 PSReadLine 模块

以**管理员身份**打开 PowerShell，执行以下命令进行安装：

```powershell
Install-Module -Name PSReadLine -Force -AllowClobber
```

如果提示是否允许从不受信任的存储库安装，输入 `Y` 并回车确认。

> **非管理员安装**：若无管理员权限，可使用 `-Scope CurrentUser` 参数仅为当前用户安装：
> ```powershell
> Install-Module -Name PSReadLine -Scope CurrentUser -Force
> ```

#### 第二步：启用核心功能（Predictive IntelliSense）

PSReadLine 最实用的功能是**智能预测**，它会根据历史记录自动提示你可能要输入的命令。执行以下命令启用：

```powershell
Set-PSReadLineOption -PredictionSource History
```

若要获得更强大的预测（包括机器学习预测），可将 `History` 改为 `HistoryAndPlugin`。

#### 第三步：保存配置（永久生效）

为了让上述配置在每次启动 PowerShell 时自动生效，需要将它们写入 PowerShell 的配置文件 `$PROFILE`。

1.  用记事本打开配置文件：
    ```powershell
    notepad $PROFILE
    ```
    如果提示找不到文件，可以先执行 `New-Item -Path $PROFILE -Type File -Force` 创建它。

2.  在打开的记事本中，粘贴以下配置内容：
    ```powershell
    # 启用基于历史记录的智能预测
    Set-PSReadLineOption -PredictionSource History

    # 将预测结果显示为美观的下拉列表 (按F2可在"行内"和"列表"视图间切换)
    Set-PSReadLineOption -PredictionViewStyle ListView

    # 设置 Tab 键唤出补全菜单
    Set-PSReadLineKeyHandler -Key "Tab" -Function MenuComplete
    ```

3.  保存文件并关闭记事本。

4.  重新加载配置文件，使更改立即生效：
    ```powershell
    . $PROFILE
    ```

### ✨ 功能一览与常用快捷键

配置完成后，你的 PowerShell 体验将有显著提升。

- **命令预测**：当你开始输入时，PSReadLine 会基于历史记录以灰色文字显示预测的命令，按 `→`（右箭头键）即可接受整条预测。
- **语法高亮**：可执行的命令会以不同颜色显示，语法错误的部分则为红色，帮助你及早发现错误。
- **智能搜索**：按 `Ctrl+R` 可以交互式搜索命令历史。

| 快捷键 | 功能说明 |
| :--- | :--- |
| `Ctrl` + `Space` | 强制显示智能提示（Predictive IntelliSense）的下拉菜单 |
| `F2` | 在“行内预测”和“列表预测”两种显示模式间切换 |
| `Ctrl` + `R` | 打开历史记录的反向搜索，输入关键词可查找之前运行过的命令 |
| `Tab` | 调出当前输入内容的补全菜单（根据前述配置） |
| `Ctrl` + `Z` | 撤销上一次的编辑操作（如果你按此配置） |

### 🛠️ 常见问题排查

- **PowerShell 启动或输入时出现黑色方块/乱码？**
  这是旧版 PSReadLine 与新版 Windows Terminal 的兼容性问题，通过更新模块即可解决。请执行：
  ```powershell
  Update-Module -Name PSReadLine -Force
  ```

- **在 VS Code 等编辑器的终端中无效？**
  因为这些终端可能在使用系统自带的旧版（v2.0.0）PSReadLine，需要手动覆盖更新。解决方法参考此[社区文章](https://www.cnblogs.com/pokersang/p/18713798)。

- **想尝试基于机器学习的智能提示？**
  除了历史记录，还可以安装 `Az.Tools.Predictor` 插件来获得更智能的补全：
  ```powershell
  Install-Module -Name Az.Tools.Predictor -Force
  Enable-AzPredictor -AllSession
  ```
  然后在 `$PROFILE` 中将 `-PredictionSource` 设置为 `HistoryAndPlugin`。