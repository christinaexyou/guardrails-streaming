# Streaming
Since vLLM supports streaming chat completions, you can configure Guardrails to stream tokens as well.

## Configuration
To activate streaming on guardrails, add `"stream": true` to the payload sent to the `v1/chat/completions` endpoint of the guardrails gateway:

## Setup
This demo is a proof of concept based on the pre-existing [TrustyAI Lemonade Stand](https://github.com/trustyai-explainability/trustyai-llm-demo/tree/main/lemonade-stand-demo/guardrails) demo. It utilizes an experimental [vLLM Orchestrator Gateway](https://github.com/trustyai-explainability/vllm-orchestrator-gateway) image that enables streaming chat completions. The following [Usage](#usage) section shows example queries for streaming chat completions using the default `whole_doc_chunker` and the experimental [`sentence` chunker](https://github.com/m-misiura/guardrails-chunkers).

## Usage

## "Is orange juice good?"
### Guardrails, `whole_doc_chunker`, `/passthrough` endpoint
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
## Guardrails, `whole_doc_chunker`, `/all` endpoint
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

## "You dotard I hate this stuff! This is a test."
### Guardrails, `sentence` chunker, `/passthrough` endpoint
```
 curl -X POST $GUARDRAILS_GATEWAY/passthrough/v1/chat/completions -v \
-H "Content-Type: application/json" \
-d '{
    "model": "phi3",
    "messages": [
        {
            "role": "user",
            "content": "You dotard I hate this stuff! This is a test."
        }
    ],
    "stream": true
}'
```

Expected output
```
data: {"id":"chat-d9b9c21830f34bc695db4a261b4e7dfc","object":"chat.completion.chunk","created":1759526169,"model":"phi3","choices":[{"index":0,"delta":{"content":"","role":"assistant","tool_calls":null},"logprobs":null,"finish_reason":null,"stop_reason":null}],"usage":null,"detections":null,"warnings":null}

data: {"id":"chat-d9b9c21830f34bc695db4a261b4e7dfc","object":"chat.completion.chunk","created":1759526169,"model":"phi3","choices":[{"index":0,"delta":{"content":" I","role":null,"tool_calls":null},"logprobs":null,"finish_reason":null,"stop_reason":null}],"usage":null,"detections":null,"warnings":null}

data: {"id":"chat-d9b9c21830f34bc695db4a261b4e7dfc","object":"chat.completion.chunk","created":1759526169,"model":"phi3","choices":[{"index":0,"delta":{"content":" understand","role":null,"tool_calls":null},"logprobs":null,"finish_reason":null,"stop_reason":null}],"usage":null,"detections":null,"warnings":null}

data: {"id":"chat-d9b9c21830f34bc695db4a261b4e7dfc","object":"chat.completion.chunk","created":1759526169,"model":"phi3","choices":[{"index":0,"delta":{"content":" that","role":null,"tool_calls":null},"logprobs":null,"finish_reason":null,"stop_reason":null}],"usage":null,"detections":null,"warnings":null}
```
### Guardrails, `sentence` chunker, `/hap` endpoint
```
curl -X POST $GUARDRAILS_GATEWAY/hap/v1/chat/completions -v \
-H "Content-Type: application/json" \
-d '{
    "model": "phi3",
    "messages": [
        {
            "role": "user",
            "content": "You dotard I hate this stuff! This is a test."
        }
    ],
    "stream": true
}'
```

Expected output:
```
data: {"id":"747530a331ef4f42a274613fc52e9ba8","object":"chat.completion.chunk","created":1759526855,"model":"phi3","choices":[],"usage":{"completion_tokens":0,"prompt_tokens":13,"total_tokens":0},"detections":{"input":[{"message_index":0,"results":[{"start":0,"end":29,"text":"You dotard I hate this stuff!","detection_type":"LABEL_1","detection":"sequence_classifier","detector_id":"hap","score":0.8637838959693909}]}],"output":null},"warnings":[{"message":"Unsuitable input detected. Please check the detected entities on your input and try again with the unsuitable input removed.","type":"UNSUITABLE_INPUT"}]}
```
