# llm api

本仓库主要用于记录调用大模型的示例代码, 主要涉及两方面:

- 大模型提供商/中转提供商: openai, claude, gemini, deepseek; openrouter, wildcard

- 调用方法: curl, requests, openai-sdk, provider-sdk, langchain

关于 provider-sdk: [openai-python-sdk](https://github.com/openai/openai-python) 的某些 API 规范 (例如 `/chat/completion`) 已经是通用标准. 但各大厂实际上也提供了自己的一套 API (不同于 openai 的接口规范), 但基本都会提供适配 `/chat/completion` 的主要参数的一个接口. 很多中转提供商所给的 API 通常也都只支持 openai 这一套的部分接口, 而不支持 provider-sdk.

这里的 provider-sdk 指的是: [anthropic-python-sdk](https://github.com/anthropics/anthropic-sdk-python), [google-python-sdk](https://github.com/googleapis/python-genai)

langchain 目前推荐使用 `init_model` 来简化大模型 API 的获取, 但对于只能通过中转服务提供商 API Key 调用的情况, `init_model` 可能无法直接使用

# openai-sdk

(更新时间: 2025/07/08) openai 提供的 endpoint 主要有如下

- `v1/chat/completions`: 最经典的接口定义, 大多数 provider 与中转也只支持这个接口定义(具体的参数会与openai的官方接口有删减/增加)
- `v1/response`: openai 新推出的 API, 旨在提供比 `v1/chat/completions` 更强大的功能, 并计划在 2026 年开始替代掉 `v1/assistants`
- `v1/embeddings`: embedding 接口
- `v1/assistants`: 计划弃用
- `v1/realtime`: TODO: 语音输出?
- `v1/batch`: TODO: 非实时接口
- `v1/fine-tuning`: 微调接口
- `v1/images/generations`: 图像生成
- `v1/images/edits`: 图像编辑
- `v1/audio/speech`: 语音合成
- `v1/audio/transcriptions`: 语音转录
- `v1/audio/translations`: 语音翻译?
- `v1/moderations`: 识别内容是否符合使用政策
- `v1/completions`: 弃用

总的来说, 最主要的接口是 `v1/chat/completions`

## chat/completions

TODO

<table style="width: 100%;">
  <tr>
    <th align="center">请求参数</th>
    <th align="center">解释</th>
    <th align="center">Openai</th>
    <th align="center">OpenRouter</th>
    <th align="center">Wildcard</th>
  </tr>
  <tr>
    <td align="center">messages</td>
    <td align="center"></td>
    <td align="center">✅</td>
    <td align="center">✅</td>
    <td align="center">✅</td>
  </tr>
  <tr>
    <td align="center">Agent</td>
    <td align="center"></td>
    <td align="center">✅</td>
    <td align="center">❌</td>
    <td align="center">✅</td>
  </tr>
  <tr>
    <td align="center">Workflow</td>
    <td align="center">✅</td>
    <td align="center">❌</td>
    <td align="center">✅</td>
    <td align="center">❌</td>
  </tr>
  <tr>
    <td align="center">Observability</td>
    <td align="center">✅</td>
    <td align="center">✅</td>
    <td align="center">❌</td>
    <td align="center">❌</td>
  </tr>
  <tr>
    <td align="center">Enterprise Feature (SSO/Access control)</td>
    <td align="center">✅</td>
    <td align="center">❌</td>
    <td align="center">❌</td>
    <td align="center">❌</td>
  </tr>
  <tr>
    <td align="center">Local Deployment</td>
    <td align="center">✅</td>
    <td align="center">✅</td>
    <td align="center">✅</td>
    <td align="center">❌</td>
  </tr>
</table>
