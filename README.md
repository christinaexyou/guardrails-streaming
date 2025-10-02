# Streaming
Since vLLM supports streaming chat completions, you can configure Guardrails to stream tokens as well.

## Configuration
To activate streaming on guardrails, add `"stream": true` to the payload sent to the `v1/chat/completions` endpoint of the guardrails gateway:

```
curl -X POST $GUARDRAILS_GATEWAY/all/v1/chat/completions -v \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $TOKEN" \
-d '{
    "model": "phi3",
    "messages": [
        {
            "role": "user",
            "content": "What is the capital of Massachusetts"
        }
    ],
    "stream": true
}'
```

Expected output:

```
data: {"id":"chat-395b6dbcacb749a993699fe8956cd201","object":"chat.completion.chunk","created":1759439082,"model":"phi3","choices":[{"index":0,"delta":{"content":" is","role":null,"tool_calls":null},"logprobs":null,"finish_reason":null,"stop_reason":null}],"usage":null,"detections":null,"warnings":null}

data: {"id":"chat-395b6dbcacb749a993699fe8956cd201","object":"chat.completion.chunk","created":1759439082,"model":"phi3","choices":[{"index":0,"delta":{"content":" the","role":null,"tool_calls":null},"logprobs":null,"finish_reason":null,"stop_reason":null}],"usage":null,"detections":null,"warnings":null}

data: {"id":"chat-395b6dbcacb749a993699fe8956cd201","object":"chat.completion.chunk","created":1759439082,"model":"phi3","choices":[{"index":0,"delta":{"content":" largest","role":null,"tool_calls":null},"logprobs":null,"finish_reason":null,"stop_reason":null}],"usage":null,"detections":null,"warnings":null}
...
data: {"id":"chat-395b6dbcacb749a993699fe8956cd201","object":"chat.completion.chunk","created":1759439082,"model":"phi3","choices":[],"usage":null,"detections":{"input":null,"output":[{"choice_index":0,"results":[]}]},"warnings":null}

data: 
```
