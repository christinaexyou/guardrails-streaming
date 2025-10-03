# Streaming
Since vLLM supports streaming chat completions, you can configure Guardrails to stream tokens as well.

## Configuration
To activate streaming on guardrails, add `"stream": true` to the payload sent to the `v1/chat/completions` endpoint of the guardrails gateway:

## Usage

## "Is orange juice good?"
### Guardrails, `/passthrough` endpoint
```
curl POST $GUARDRAILS_GATEWAY/passthrough/v1/chat/completions -v \
-H "Content-Type: application/json" \
-d '{
    "model": "phi3",
    "messages": [
        {
            "role": "user",
            "content": "Is orange juice good?"
        }
    ],
    "stream": true
}'
```

Expected output:
```
data: {"id":"chat-6cceec637ff94cdd96c250e621c008a7","object":"chat.completion.chunk","created":1759524206,"model":"phi3","choices":[{"index":0,"delta":{"content":"","role":"assistant","tool_calls":null},"logprobs":null,"finish_reason":null,"stop_reason":null}],"usage":null,"detections":null,"warnings":null}

data: {"id":"chat-6cceec637ff94cdd96c250e621c008a7","object":"chat.completion.chunk","created":1759524206,"model":"phi3","choices":[{"index":0,"delta":{"content":" Yes","role":null,"tool_calls":null},"logprobs":null,"finish_reason":null,"stop_reason":null}],"usage":null,"detections":null,"warnings":null}

data: {"id":"chat-6cceec637ff94cdd96c250e621c008a7","object":"chat.completion.chunk","created":1759524206,"model":"phi3","choices":[{"index":0,"delta":{"content":",","role":null,"tool_calls":null},"logprobs":null,"finish_reason":null,"stop_reason":null}],"usage":null,"detections":null,"warnings":null}

data: {"id":"chat-6cceec637ff94cdd96c250e621c008a7","object":"chat.completion.chunk","created":1759524206,"model":"phi3","choices":[{"index":0,"delta":{"content":" orange","role":null,"tool_calls":null},"logprobs":null,"finish_reason":null,"stop_reason":null}],"usage":null,"detections":null,"warnings":null}

data: {"id":"chat-6cceec637ff94cdd96c250e621c008a7","object":"chat.completion.chunk","created":1759524206,"model":"phi3","choices":[{"index":0,"delta":{"content":" ju","role":null,"tool_calls":null},"logprobs":null,"finish_reason":null,"stop_reason":null}],"usage":null,"detections":null,"warnings":null}
...
```
## Guardrails, `/all` endpoint
```
curl POST $GUARDRAILS_GATEWAY/all/v1/chat/completions -v \
-H "Content-Type: application/json" \
-d '{
    "model": "phi3",
    "messages": [
        {
            "role": "user",
            "content": "Is orange juice good?"
        }
    ],
    "stream": true
}'
```

Expected output:
```
data: {"id":"71ad9d973e1b42ac896fa8c489cdc816","object":"chat.completion.chunk","created":1759524359,"model":"phi3","choices":[],"usage":{"completion_tokens":0,"prompt_tokens":6,"total_tokens":0},"detections":{"input":[{"message_index":0,"results":[{"start":3,"end":9,"text":"orange","detection_type":"regex","detection":"custom-regex","detector_id":"regex_competitor","score":1.0}]}],"output":null},"warnings":[{"type":"UNSUITABLE_INPUT","message":"Unsuitable input detected. Please check the detected entities on your input and try again with the unsuitable input removed."}]}
```
