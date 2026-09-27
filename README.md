# Codex Session Auth

> 免验证码登录 Codex · 打开即用的纯前端工具

通过 ChatGPT 的 Session Token 一键生成 Codex CLI 的凭证文件 (`~/.codex/auth.json`)，跳过 OAuth / 验证码流程，适用于网络受限或无法完成浏览器登录的场景。

## 特性

- **纯前端实现**：单文件 HTML，所有解析、生成均在浏览器本地完成，token 不会上传到任何服务器
- **多平台命令**：自动生成 Windows PowerShell / Linux Bash / macOS Bash 三种平台的写入命令
- **智能解析**：支持粘贴完整 Session JSON，也支持仅粘贴 `accessToken` 字符串
- **自动提取 account_id**：从 JWT payload 中解析 `chatgpt_account_id`，无需手动填写
- **PowerShell 5.1 兼容**：使用 .NET API 强制写入无 BOM 的 UTF-8，避免 codex 报 `expected value at line 1 column 1` 错误

## 使用方法

### 1. 打开页面

直接在浏览器中打开 [index.html](index.html) 即可，无需任何依赖、无需构建。

也可以通过任意静态服务器托管，例如：

```bash
# Python
python -m http.server 8000

# Node.js (需先 npm i -g http-server)
http-server .
```

### 2. 获取 Session Token

1. 在浏览器中登录 [ChatGPT](https://chatgpt.com)
2. 在工具页面点击「打开 Session 接口」，会跳转到 `https://chatgpt.com/api/auth/session`
3. 复制接口返回的整段 JSON，形如：
   ```json
   {
     "user": { ... },
     "accessToken": "eyJhbGciOiJSUzI1NiIs...",
     "expires": "2026-..."
   }
   ```

### 3. 粘贴并执行命令

1. 将 JSON 粘贴到页面输入框（也可以只粘贴 `accessToken` 的值）
2. 选择对应操作系统的标签页（Windows PowerShell / Linux Bash / macOS Bash）
3. 点击「复制」按钮，在终端执行命令
4. 命令会在 `~/.codex/auth.json` 写入凭证文件

完成后即可直接使用 Codex CLI，无需再走交互式登录。

## 生成的凭证格式

```json
{
  "auth_mode": "chatgpt",
  "OPENAI_API_KEY": null,
  "tokens": {
    "id_token": "<accessToken>",
    "access_token": "<accessToken>",
    "refresh_token": "",
    "account_id": "<从 JWT 提取的 chatgpt_account_id>"
  },
  "last_refresh": "<当前时间 ISO 字符串>"
}
```

## 项目结构

```
codex-session-auth/
├── index.html       # 主页面 (单文件应用)
├── README.md
└── docs/
    └── prd.md       # 需求文档
```

## 安全说明

- 所有 token 解析、命令生成完全在浏览器本地完成，**不会向任何服务器发送 token**
- 建议在可信设备上使用，并在使用完成后关闭页面
- Session Token 具有时效性 (`expires` 字段)，过期后需要重新获取

## License

MIT
