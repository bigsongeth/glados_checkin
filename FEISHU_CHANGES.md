# 飞书 Webhook 功能集成 - 修改总结

## 概述
本次更新为 glados_checkin 项目添加了飞书机器人 Webhook 的支持，使用户能够通过飞书群组机器人接收每日自动签到的通知消息。

## 修改的文件

### 1. `main.js` 
**添加飞书 Webhook 支持**

在 `notify()` 函数中增加了新的条件分支（第 84-105 行）：

```javascript
} else if (option.startsWith('feishu:')) {
  const feishuWebhookUrl = option.split(':').slice(1).join(':')
  await fetch(feishuWebhookUrl, {
    method: 'POST',
    headers: { 'content-type': 'application/json' },
    body: JSON.stringify({
      msg_type: 'post',
      content: {
        post: {
          zh_cn: {
            title: notice[0],
            content: [
              notice.map((line) => ({
                tag: 'text',
                text: line
              }))
            ]
          }
        }
      }
    }),
  })
}
```

**关键特性：**
- 识别 `feishu:` 前缀的通知配置
- 提取完整的 Webhook URL（支持 URL 中包含冒号）
- 使用飞书的 `post` 消息类型，支持富文本格式
- 中文界面显示（`zh_cn`）
- 自动格式化签到信息，每行单独显示

### 2. `readme.md`
**更新使用说明**

- 添加飞书通知方式到支持列表
- 添加飞书官方链接
- 说明飞书的配置格式：`feishu:{webhook_url}`

### 3. `FEISHU_SETUP.md`（新增）
**详细的飞书集成指南**

包含以下内容：
- 快速开始步骤
- 创建飞书群组机器人的详细说骤
- GitHub Secret 配置方法
- 多渠道通知的配置示例
- 故障排除指南
- 常见错误和解决方案
- 安全建议

## 使用方法

### 基本配置
1. 在飞书中创建群组机器人，获取 Webhook URL
2. 在 GitHub 仓库的 Secrets 中添加：
   ```
   NOTIFY=feishu:{webhook_url}
   ```
3. 保存后，工作流将每天自动签到并发送通知

### 配置多个通知渠道（可选）
```
NOTIFY=feishu:https://open.feishu.cn/open-apis/bot/v2/hook/xxx
pushplus:your_token
wxpusher:token:uid
```

## 技术细节

### Webhook 消息格式
飞书接收的 JSON 格式：
```json
{
  "msg_type": "post",
  "content": {
    "post": {
      "zh_cn": {
        "title": "Checkin OK",
        "content": [[
          {"tag": "text", "text": "Checkin OK"},
          {"tag": "text", "text": "...action message..."},
          {"tag": "text", "text": "Left Days X"}
        ]]
      }
    }
  }
}
```

### 兼容性
- ✅ 与现有的通知系统完全兼容
- ✅ 支持与其他通知渠道并行使用
- ✅ 不影响现有功能
- ✅ 遵循 GitHub Actions 安全最佳实践

## 变更日志

| 版本 | 日期 | 描述 |
|------|------|------|
| 1.2.0 | 2026-07-01 | 新增飞书 Webhook 支持 |

## 提交信息

建议的 Git 提交信息：
```
feat: 新增飞书机器人 Webhook 通知支持

- 在 notify() 函数中添加飞书 webhook 处理
- 支持 feishu:{webhook_url} 配置格式
- 添加详细的飞书集成指南 FEISHU_SETUP.md
- 更新 README.md 文档

Closes #[issue_number]
```

## 下一步建议

1. **测试**：手动运行工作流确保飞书通知正常工作
2. **反馈**：如有问题，提交 Issue 或 PR
3. **扩展**：可考虑添加更多飞书消息类型支持（如卡片消息）

## 注意事项

1. **安全性**：不要在公开渠道分享 Webhook URL
2. **速率限制**：飞书 API 有速率限制，但日常使用无需担心
3. **错误处理**：如果 Webhook 请求失败，错误信息会显示在 GitHub Actions 日志中
4. **支持版本**：本功能与所有现有的通知方式兼容

## 参考资源

- [飞书官方开发文档](https://open.feishu.cn/)
- [飞书机器人 API](https://open.feishu.cn/document/client-docs/bot-v3/add-custom-bot)
- [项目 README](./readme.md)
- [飞书集成指南](./FEISHU_SETUP.md)
