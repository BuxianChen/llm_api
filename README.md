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

openai API [https://platform.openai.com/docs/api-reference/chat](https://platform.openai.com/docs/api-reference/chat)

openrouter API: [https://openrouter.ai/docs/api-reference/chat-completion](https://openrouter.ai/docs/api-reference/chat-completion)

claude openai-compatility: [https://docs.anthropic.com/en/api/openai-sdk](https://docs.anthropic.com/en/api/openai-sdk)

gemini openai-compatility: [https://ai.google.dev/gemini-api/docs/openai](https://ai.google.dev/gemini-api/docs/openai)

deepseek API: [https://api-docs.deepseek.com/zh-cn/api/create-chat-completion](https://api-docs.deepseek.com/zh-cn/api/create-chat-completion)

注意: 这里只是梳理接口定义, 并非所有的模型都支持下面的参数

<table style="width: 100%;">
  <tr>
    <th align="center">请求参数</th>
    <th align="center">openai参数说明</th>
    <th align="center">openai</th>
    <th align="center">OpenRouter参数说明</th>
    <th align="center">OpenRouter</th>
    <th align="center">gemini参数说明</th>
    <th align="center">gemini</th>
  </tr>
  <tr>
    <td align="center">messages</td>
    <td align="center">对话历史</td>
    <td align="center">✅</td>
    <td align="center">支持部分类型</td>
    <td align="center">❌</td>
    <td align="center">支持</td>
    <td align="center">✅</td>
  </tr>
  <tr>
    <td align="center">audio</td>
    <td align="center">是否返回语音</td>
    <td align="center">✅</td>
    <td align="center">不支持</td>
    <td align="center">❌</td>
    <td align="center">TODO</td>
    <td align="center">❓</td>
  </tr>
</table>

### openai-python-sdk

```python
import os
import openai
from dotenv import load_dotenv

load_dotenv()

providers = ["GEMINI", "DEEPSEEK", "OPENROUTER", "WILDCARD"]
provider = "OPENROUTER"
model = os.environ[f"{provider}_MODEL"]
base_url = os.environ[f"{provider}_BASE_URL"]
api_key = os.environ[f"{provider}_API_KEY"]

print(f"provider: {provider}\nmodel: {model}\nbase_url: {base_url}")

client = openai.OpenAI(
    base_url=base_url,
    api_key=api_key,
)

response = client.chat.completions.create(
    model=model,
    messages=[
        {"role": "user", "content": "hello"}
    ]
)
print(response)
```

### langchain


# OpenRouter

所有支持的模型可以在 [https://openrouter.ai/models](https://openrouter.ai/models) 找到, 并且可以筛选哪些模型支持特定的参数 (例如工具调用, context-length 等)

# google-genai sdk

```python
from google.genai import Client
from google.genai.types import Content, Part, GenerateContentConfig
from google.genai.types import Tool, ToolCodeExecution, FunctionDeclaration, Schema, GoogleSearch


def get_temperature(city):
    weather_map = {
        "newyork": 23.2,
        "beijing": 30.0,
        "shanghai": 12.9,
        "guangzhou": 13.0
    }
    c = city.replace(" ", "").lower()
    if c in weather_map:
        return weather_map[c]
    else:
        return None


client = Client(vertexai=False, api_key="...")

# 似乎只同时支持一类工具
# response = client.models.generate_content(...)
response = await client.aio.models.generate_content(
    model='gemini-2.5-flash',
    contents=[
        Content(
            parts=[Part(text="help me caculate the average temperature of these cities: New York, Beijing, Shanghai")]
        )
    ],
    config=GenerateContentConfig(
        tools=[
            # Tool(
            #     code_execution=ToolCodeExecution()
            # ),
            Tool(
                function_declarations=[
                    FunctionDeclaration(
                        description="given a city name, get Celsius temperature, if the temperature not available, will return None",
                        name="get_temperature",
                        parameters={
                            "type": "object",
                            "properties": {
                                "city": {
                                    "type": "string",
                                    "description": "city name",
                                }
                            },
                            "required": ["city"],
                        },
                    )
                ]
            ),
            # Tool(
            #     google_search=GoogleSearch()
            # ),
        ]
    )
)
```