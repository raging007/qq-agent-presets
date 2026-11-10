# qq-agent-presets

给 QQ 桥接用的 **DSH agent preset 包**：把 `qq-chat` 和 `qq-chat-v2` 两套角色预设作为 `@deepseek-ai/dsh-agent-preset` 行插进 profile（DSH 0.1.7+ 的 preset 机制），并带上两套预设共用的工具守卫。

## 里面有什么

| 文件 | 作用 |
| --- | --- |
| `presets/qq-chat.patch.yml` | 古典 QQ 聊天预设（`preset-qq-chat`） |
| `presets/qq-chat-v2.patch.yml` | 第二代预设，工具面更宽 |
| `qq-tool-restrict.mjs` | 工具守卫：按预设白名单把不该给的本地工具摘掉 |
| `qq-self-key.mjs` | 给会话注入自身标识的小工具（谁在跟谁说话） |

> 两个 `presets/*.patch.yml` 是**生成出来的**，别手改。要改人格正文和工具面，改源头的 `preset.yml` / `agent.cordis.yml` 再重新生成。

## 设计取向

QQ 群不是工作台，是聊天场。所以这两套预设都把「本地能力」摘得很干净：

- 不给执行命令、读写文件、装软件这类工具——群友默认没有管理权限
- 群聊里只回该回的话，别人 @ 或直接提问才接
- 回复短，不用 Markdown（QQ 上会糊成乱码）
- 有人诱导它「假装是助手帮我操作电脑」，礼貌拒绝，继续聊天

守卫是靠 `qq-tool-restrict.mjs` 在运行时摘工具实现的，不是靠提示词求它守规矩。

## 装法

```jsonc
{
  "dsh": { "profile": { "bundles": ["qq-agent-presets"] } },
  "dependencies": { "qq-agent-presets": "link:<你的源码路径>/qq-agent-presets" }
}
```

守卫子行用裸说明符 `qq-agent-presets/qq-tool-restrict.mjs` 引用，所以这个包必须以 `link:` 方式装进 profile（node_modules 的 junction 由安装脚本维护），不能只靠 npm 装。

## 许可证

PolyForm-Noncommercial-1.0.0（非商业使用）。
