# 功能简介
## 智能体链接
[Coze商店链接](https://www.coze.cn/store/agent/7618101640721186826?bot_id=true)
## 新增功能：语言水平询问机制
首次对话时，Agent 会主动询问用户的德语水平（A1/A2/B1/B2/C1/C2）
使用友好的提示语，清晰说明各等级的定义
等待用户回复后，再进行针对性的精读分析

## 分级分析策略
根据用户语言水平，智能调整分析深度和表述方式：
### A1-A2（初级）
使用通俗易懂的语言，避免专业术语
重点讲解基础语法：名词性和数、冠词、基本时态、简单句结构
提供大量简单例句和实用搭配
学习建议侧重基础练习、记忆方法、初级资源推荐
### B1-B2（中级）
适度使用语法术语，并给出简明解释
重点讲解复杂语法：从句、被动语态、虚拟式、介词搭配
词汇解析增加同义词辨析、固定搭配
学习推荐中级教材、德语媒体和练习方法
### C1-C2（高级）
使用专业语言学术语，进行深入分析
重点讲解高级语法：修辞手法、语篇衔接、语用学
词汇解析深入词源、语义场、文学修辞
学习推荐高级资源、学术著作、哲学原著

## 功能亮点
智能识别：自动记住用户的语言水平，后续对话无需重复询问
动态调整：根据水平实时调整分析深度，确保内容适合用户
专业分级：严格遵循 CEFR 标准，分级科学合理
用户体验：首次对话友好询问，后续自动适配

# 项目结构说明

# 本地运行
## 运行流程
`bash scripts/local_run.sh -m flow`

## 运行节点
`bash scripts/local_run.sh -m node -n node_name`

# 启动HTTP服务
`bash scripts/http_run.sh -m http -p 5000`

# 调用示例
## 公共API Token
请访问：[(3006)公共API Token](https://wiki.berryyang.com/zh/sisukurse/nachrichten/archiv/3006)

## cURL
```
curl --location --request POST "https://4ypzgb5hb3.coze.site/stream_run" \
  --header "Authorization: Bearer <YOUR_TOKEN>" \
  --header "Content-Type: application/json" \
  --data '{
      "content": {
        "query": {
          "prompt": [
            {
              "type": "text",
              "content": {
                "text": ""
              }
            }
          ]
        }
      },
      "type": "query",
      "session_id":"SJ6UJmAsrP-n4wg46uB9N",
      "project_id": 7618079515360935979
    }'
```

## Python
```
import json
import requests
url = "https://4ypzgb5hb3.coze.site/stream_run"
headers = {
  "Authorization": "Bearer <YOUR_TOKEN>",
  "Content-Type": "application/json",
  "Accept": "text/event-stream",
}
payload = json.loads(r'''{
  "content": {
    "query": {
      "prompt": [
        {
          "type": "text",
          "content": {
            "text": ""
          }
        }
      ]
    }
  },
  "type": "query",
  "session_id": "VUpKFBZQyXicSpGFzDtYt",
  "project_id": "7618079515360935979"
}''')
response = requests.post(url, headers=headers, json=payload, stream=True)
print("status:", response.status_code)
try:
  response.raise_for_status()
except Exception:
  print(response.text)
  raise
for line in response.iter_lines(decode_unicode=True):
  if line and line.startswith("data:"):
    data_text = line[5:].strip()
    try:
      parsed = json.loads(data_text)
      print(json.dumps(parsed, ensure_ascii=False, indent=2))
    except Exception:
      print(data_text)
```

## NodeJS
```

async function main() {
  const url = "https://4ypzgb5hb3.coze.site/stream_run";
  const headers = {
      "Authorization": "Bearer <YOUR_TOKEN>",
      "Content-Type": "application/json",
      "Accept": "text/event-stream"
  };
  const res = await fetch(url, {
    method: "POST",
    headers,
    body: JSON.stringify({"content":{"query":{"prompt":[{"type":"text","content":{"text":""}}]}},"type":"query","session_id":"720OVPzkhwTEJZymoKmRH","project_id":"7618079515360935979"})
  });
  console.log("status:", res.status);
  if (!res.ok) {
    const errText = await res.text();
    console.log(errText);
    return;
  }
  if (!res.body) {
    console.log("No response body");
    return;
  }
  const reader = res.body.getReader();
  const decoder = new TextDecoder();
  let buffer = "";
  while (true) {
    const { done, value } = await reader.read();
    if (done) break;
    buffer += decoder.decode(value, { stream: true });
    const blocks = buffer.split("\n\n");
    buffer = blocks.pop() ?? "";
    for (const block of blocks) {
      const dataLines = block
        .split("\n")
        .filter(line => line.startsWith("data:"))
        .map(line => line.slice(5).trim());
      if (dataLines.length === 0) continue;
      const dataText = dataLines.join("\n");
      try {
        const parsed = JSON.parse(dataText);
        console.log(JSON.stringify(parsed, null, 2));
      } catch (e) {
        console.log(dataText);
      }
    }
  }
}

main().catch(err => {
  console.error(err);
});
```
