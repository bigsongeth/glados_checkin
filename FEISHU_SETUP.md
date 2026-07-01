# 飞书 Webhook 集成指南

本项目现已支持通过飞书机器人接收自动签到的通知消息。

## 快速开始

### 步骤 1：创建飞书群组机器人

1. 打开飞书客户端，创建或打开一个群组
2. 点击群组设置 → 群组机器人
3. 点击"添加机器人"
4. 选择"自定义机器人"
5. 设置机器人名称（如 "GLaDOS 签到助手"）
6. 配置权限（需要发送消息权限）
7. 创建完成后，复制 **Webhook URL**

### 步骤 2：配置 GitHub Secret

1. 进入你 Fork 的仓库
2. 点击 Settings → Secrets and variables → Actions
3. 新建 Secret `NOTIFY`，内容填写：
   ```
   feishu:{your_webhook_url_here}
   ```
   
   例如：
   ```
   feishu:https://open.feishu.cn/open-apis/bot/v2/hook/xxxxxxxxxxxxxxx
   ```

4. 保存 Secret

### 步骤 3：启用 Actions

1. 进入 Actions 标签页
2. 启用 Workflows
3. 工作流将在每天北京时间 00:10 自动执行，完成签到后会发送通知到飞书群组

## 高级配置

### 配置多个通知渠道

如果需要同时使用飞书和其他通知方式，可以在 `NOTIFY` secret 中配置多行（每行一个）：

```
feishu:https://open.feishu.cn/open-apis/bot/v2/hook/xxxxxxxxxxxxxxx
pushplus:your_pushplus_token_here
wxpusher:your_token:your_uid
```

### 消息格式

飞书通知将包含以下信息：
- **标题**：签到结果（"Checkin OK" 或 "Checkin Error"）
- **内容**：详细的签到信息，包括
  - 账户响应信息
  - 剩余天数
  - 错误详情（如果失败）
  - GitHub 工作流链接

## 故障排除

### 收不到通知消息

1. **检查 Webhook URL 是否正确**
   - 确保复制的是完整的 URL
   - URL 不应该包含额外的符号或空格

2. **检查机器人权限**
   - 确保机器人有发送消息的权限
   - 确保机器人没有被禁用

3. **查看 Actions 日志**
   - 进入 Actions → 最近的工作流运行
   - 检查日志中是否有错误信息
   - 如果使用 `console:log` 可以看到完整的通知内容

4. **测试机器人**
   - 可以手动触发 workflow：Actions → run → Run workflow
   - 这样可以立即看到结果

### 常见错误

| 错误 | 原因 | 解决方案 |
|------|------|--------|
| 403 Forbidden | Webhook URL 已失效或权限不足 | 重新创建机器人，复制新的 Webhook URL |
| 404 Not Found | Webhook URL 不存在 | 确认是否正确复制了完整的 URL |
| Invalid JSON | 消息格式错误 | 检查代码是否正常，查看日志获取详细信息 |

## API 文档

飞书机器人 API：https://open.feishu.cn/document/client-docs/bot-v3/add-custom-bot

支持的消息类型：
- `text`：文本消息
- `post`：富文本消息（本项目使用）
- `image`：图片消息
- `file`：文件消息
- 等等

## 安全建议

1. **不要将 Webhook URL 公开分享**
   - 任何人拥有 URL 都可以向你的群组发送消息
   - 将其存储在 GitHub Secrets 中

2. **定期轮换 Webhook URL**
   - 如果怀疑 URL 被泄露，重新创建机器人

3. **限制机器人权限**
   - 只赋予必要的权限（发送消息即可）

## 联系支持

如有问题，请：
1. 查看本项目的 Issues
2. 检查飞书官方文档：https://open.feishu.cn/
3. 提交 Bug Report 或 Feature Request
