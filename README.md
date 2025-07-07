# llm api

本仓库主要用于记录调用大模型的示例代码, 主要涉及两方面:

- 大模型提供商/中转提供商: openai, claude, gemini, deepseek; openrouter, wildcard

- 调用方法: curl, requests, openai-sdk, provider-sdk, langchain

关于 provider-sdk: [openai-python-sdk](https://github.com/openai/openai-python) 的某些 API 规范 (例如 `/chat/completion`) 已经是通用标准. 但各大厂实际上也提供了自己的一套 API (不同于 openai 的接口规范), 但基本都会提供适配 `/chat/completion` 的主要参数的一个接口. 很多中转提供商所给的 API 通常也都只支持 openai 这一套的部分接口, 而不支持 provider-sdk.

这里的 provider-sdk 指的是: [anthropic-python-sdk](https://github.com/anthropics/anthropic-sdk-python), [google-python-sdk](https://github.com/googleapis/python-genai)

langchain 目前推荐使用 `init_model` 来简化大模型 API 的获取, 但对于只能通过中转服务提供商 API Key 调用的情况, `init_model` 可能无法直接使用
