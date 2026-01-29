下面是**完整可直接粘贴到 GitHub `README.md`** 的 Markdown 排版版本（已把你说的“1 里面的两段代码合并成一个”，避免出现“二单独一块”的感觉）。你直接整段复制即可。

---

# powershell-proxy-toggle

PowerShell 一键开关代理脚本（`pon` / `poff` / `ptest`）

---

## 🚀 PowerShell Proxy Toggle (pon/poff)

这是一个简单的 PowerShell 脚本，旨在通过几个短命令快速管理终端代理状态，适用于 Git / npm / Gemini CLI 等需要代理的场景。

---

## ✨ 功能特性

* **一键开启 (`pon`)**：设置 `HTTP_PROXY` / `HTTPS_PROXY` 环境变量，并设置 .NET 默认代理（影响 PowerShell 的网络请求）。
* **一键关闭 (`poff`)**：清除代理相关设置，恢复直连。
* **状态检查 (`ptest`)**：检测当前出口 IP 及国家/地区信息。
* **防乱码处理**：统一 `UTF-8` 输出，减少 Windows 终端中文乱码。

---

## 🛠️ 安装方法

### 1) 定位并打开 PowerShell 配置文件（只需一次）

在 PowerShell 中执行下面这段（**已合并成一个代码块**）：

```powershell
# 如果 $PROFILE 不存在就创建，然后打开它
if (!(Test-Path -Path $PROFILE)) { New-Item -ItemType File -Path $PROFILE -Force }
notepad $PROFILE
```

---

### 2) 注入脚本（粘贴到 $PROFILE 末尾）

将以下代码复制并粘贴到记事本末尾，按需修改端口（默认 `7890`）：

```powershell
# --- 代理设置脚本开始 ---

# 统一使用 UTF-8 编码（解决中文乱码）
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8

# 1. 开启代理
function pon {
    $addr = "http://127.0.0.1:7890"  # 修改为你的实际端口
    $env:HTTP_PROXY  = $addr
    $env:HTTPS_PROXY = $addr
    [System.Net.WebRequest]::DefaultWebProxy = New-Object System.Net.WebProxy($addr)
    Write-Host "🚀 Proxy ON: $addr" -ForegroundColor Green
}

# 2. 关闭代理
function poff {
    $env:HTTP_PROXY  = $null
    $env:HTTPS_PROXY = $null
    [System.Net.WebRequest]::DefaultWebProxy = $null
    Write-Host "🛑 Proxy OFF" -ForegroundColor Yellow
}

# 3. 状态测试
function ptest {
    Write-Host "Checking connection..." -ForegroundColor Gray
    try {
        $p = if ($env:HTTP_PROXY) { $env:HTTP_PROXY } else { $null }
        $res = Invoke-RestMethod -Uri "http://ip-api.com/json" -Proxy $p -TimeoutSec 5
        Write-Host "Current IP: $($res.query) ($($res.country))" -ForegroundColor Cyan
    } catch {
        Write-Host "❌ Failed! Proxy software is not running or port is wrong." -ForegroundColor Red
    }
}

# --- 代理设置脚本结束 ---
```

---

### 3) 使配置生效

保存并关闭记事本，然后重启 PowerShell 或执行：

```powershell
. $PROFILE
```

---

## ✅ 命令说明

| 命令      | 说明                              |
| ------- | ------------------------------- |
| `pon`   | 开启代理环境，适用于 Gemini CLI、Git、npm 等 |
| `poff`  | 关闭代理，切回直连                       |
| `ptest` | 检查代理是否生效（获取出口 IP/国家）            |

---


