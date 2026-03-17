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
