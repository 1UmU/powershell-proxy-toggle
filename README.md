# powershell-proxy-toggle
PowerShell 一键开关代理脚本
# PowerShell Proxy Toggle (pon/poff) 🚀

这是一个简单的 PowerShell 脚本，旨在通过简单的命令（`pon`、`poff`、`ptest`）快速管理终端的代理状态。

## ✨ 功能特性

* **一键开启 (`pon`)**：快速设置 HTTP/HTTPS 代理环境变量及系统 API 代理。
* **一键关闭 (`poff`)**：清除所有代理设置，回归直连模式。
* **状态检查 (`ptest`)**：实时检测当前出口 IP 及地理位置。
* **防乱码处理**：内置 UTF-8 编码设置，解决 Windows 终端中文显示问题。

---

## 🛠️ 安装方法

### 1. 定位配置文件
在 PowerShell 中运行以下命令打开你的配置文件：
```powershell
notepad $PROFILE
如果提示找不到路径，请先运行：if (!(Test-Path -Path $PROFILE)) { New-Item -ItemType File -Path $PROFILE -Force }

### 2.  注入脚本
将以下代码复制并粘贴到记事本末尾，注意根据你的代理软件修改端口（默认为 7890）：
# --- 代理设置脚本开始 ---

# 统一使用 UTF-8 编码
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8

# 1. 开启代理
function pon {
    $addr = "[http://127.0.0.1:7890](http://127.0.0.1:7890)"  # 此处修改为你的实际端口
    $env:HTTP_PROXY = $addr
    $env:HTTPS_PROXY = $addr
    [System.Net.WebRequest]::DefaultWebProxy = New-Object System.Net.WebProxy($addr)
    Write-Host "🚀 Proxy ON: $addr" -ForegroundColor Green
}

# 2. 关闭代理
function poff {
    $env:HTTP_PROXY = $null
    $env:HTTPS_PROXY = $null
    [System.Net.WebRequest]::DefaultWebProxy = $null
    Write-Host "🛑 Proxy OFF" -ForegroundColor Yellow
}

# 3. 状态测试
function ptest {
    Write-Host "Checking connection..." -ForegroundColor Gray
    try {
        $p = if ($env:HTTP_PROXY) { $env:HTTP_PROXY } else { $null }
        $res = Invoke-RestMethod -Uri "[http://ip-api.com/json](http://ip-api.com/json)" -Proxy $p -TimeoutSec 5
        Write-Host "Current IP: $($res.query) ($($res.country))" -ForegroundColor Cyan
    } catch {
        Write-Host "❌ Failed! Proxy software is not running or port is wrong." -ForegroundColor Red
    }
}
# --- 代理设置脚本结束 ---
###3. 使配置生效
保存并关闭记事本，重启 PowerShell 或运行：
. $PROFILE

命令,说明
pon,开启代理环境，适用于 Gemini CLI、Git、npm 等。
poff,关闭代理，切回国内直连。
ptest,检查当前是否翻墙成功。












