# Anthropic Messages API

Send a structured list of input messages with text and/or image content, and the model will generate the next message in the conversation.

The Messages API can be used for either single queries or stateless multi-turn conversations.

*Learn more about the Messages API in our [user guide](<link-to-user-guide>).* (Note: Replace `<link-to-user-guide>` with the actual URL)

---

## API Endpoint

```http
POST /v1/messages
```

---

## Python Example

```python
import anthropic

# Note: Assumes the ANTHROPIC_API_KEY environment variable is set.
# Alternatively, initialize with:
# client = anthropic.Anthropic(api_key="YOUR_API_KEY")
client = anthropic.Anthropic()

try:
    message = client.messages.create(
        # Note: Using a current plausible model name.
        # Verify and replace with your desired model if different.
        model="claude-3-5-sonnet-20240620",
        max_tokens=1024,
        messages=[
            {"role": "user", "content": "Hello, world"}
        ]
    )
    print(message)

except anthropic.APIConnectionError as e:
    print("The server could not be reached")
    print(e.__cause__)  # an underlying httpx error
except anthropic.RateLimitError as e:
    print("A 429 status code was received; we should back off a bit.")
except anthropic.APIStatusError as e:
    print("Another non-200-range status code was received")
    print(e.status_code)
    print(e.response)
```

### Example Response Snippet

```json
{
  "id": "msg_013Zva2CMHLNnXjNJJKqJ2EF",
  "type": "message",
  "role": "assistant",
  "model": "claude-3-5-sonnet-20240620",
  "content": [
    {
      "type": "text",
      "text": "Hi! My name is Claude."
    }
  ],
  "stop_reason": "end_turn",
  "stop_sequence": null,
  "usage": {
    "input_tokens": 10,  # Example value
    "output_tokens": 9   # Example value
  }
}
```

*(Note: cURL and JavaScript examples were mentioned but not fully provided in the source text.)*

---

## Headers

### `anthropic-beta`

*   **Type**: `string[]`
*   **Optional**
*   Specifies beta features you want to use.
*   To use multiple betas, provide a comma-separated list (e.g., `beta1,beta2`) or specify the header multiple times.

### `anthropic-version`

*   **Type**: `string`
*   **Required**
*   The version of the Anthropic API to use (e.g., `2023-06-01`).
*   *Read more about [versioning](<link-to-versioning-docs>).* (Note: Replace `<link-to-versioning-docs>` with the actual URL)

### `x-api-key`

*   **Type**: `string`
*   **Required**
*   Your unique API key for authentication.
*   Obtain your API key from the [Anthropic Console](<link-to-console>). (Note: Replace `<link-to-console>` with the actual URL) Keys are scoped to a Workspace.

---

## Request Body (`application/json`)

### `max_tokens`

*   **Type**: `integer`
*   **Required**
*   The maximum number of tokens to generate before stopping. The model may stop naturally before reaching this limit.
*   Different models have different maximum values. See [models documentation](<link-to-models-docs>) for details. (Note: Replace `<link-to-models-docs>` with the actual URL)
*   **Constraint**: Must be greater than 1 (`> 1`).

### `messages`

*   **Type**: `object[]`
*   **Required**
*   An array of input message objects, representing the conversation history.
*   Models are trained on alternating `user` and `assistant` roles. Consecutive messages of the same role will be combined.
*   Each message object must have a `role` (`user` or `assistant`) and `content`.
*   If the final message has the `assistant` role, the model's response will be a continuation of that message's content.
*   **Content Structure**:
    *   `content` can be a single string (shorthand for a single text block).
    *   `content` can be an array of content blocks (e.g., `text` or `image`).

    ```json
    // Equivalent ways to specify text content:
    {"role": "user", "content": "Hello, Claude"}
    {"role": "user", "content": [{"type": "text", "text": "Hello, Claude"}]}
    ```

    *   **Image Content (Claude 3+):** You can include `image` content blocks.
        *   Supported `source.type`: `base64`
        *   Supported `media_type`: `image/jpeg`, `image/png`, `image/gif`, `image/webp`

        ```json
        {"role": "user", "content": [
          {
            "type": "image",
            "source": {
              "type": "base64",
              "media_type": "image/jpeg",
              "data": "/9j/4AAQSkZJRg..." // Base64 encoded image data
            }
          },
          {"type": "text", "text": "What is in this image?"}
        ]}
        ```

*   **Examples**:
    *   *Single User Message:*
        ```json
        [{"role": "user", "content": "Hello, Claude"}]
        ```
    *   *Multi-turn Conversation:*
        ```json
        [
          {"role": "user", "content": "Hello there."},
          {"role": "assistant", "content": "Hi, I'm Claude. How can I help you?"},
          {"role": "user", "content": "Can you explain LLMs in plain English?"}
        ]
        ```
    *   *Constraining Assistant Response:*
        ```json
        [
          {"role": "user", "content": "What's the Greek name for Sun? (A) Sol (B) Helios (C) Sun"},
          {"role": "assistant", "content": "The best answer is ("} // Model will continue from here
        ]
        ```
*   *See more [input examples](<link-to-input-examples>).* (Note: Replace `<link-to-input-examples>` with the actual URL)
*   **Note**: Use the top-level `system` parameter for system prompts; there is no `"system"` role for messages.
*   **Limit**: 100,000 messages per request (though token limits are usually reached first).

### `model`

*   **Type**: `string`
*   **Required**
*   The model ID to use for completion (e.g., `claude-3-5-sonnet-20240620`).
*   See [models documentation](<link-to-models-docs>) for available models. (Note: Replace `<link-to-models-docs>` with the actual URL)
*   **Constraint**: String length must be between 1 and 256 characters.

### `metadata`

*   **Type**: `object`
*   **Optional**
*   An object containing metadata about the request (e.g., `{"user_id": "user-123"}`).

### `stop_sequences`

*   **Type**: `string[]`
*   **Optional**
*   A list of custom text sequences that will cause the model to stop generating.
*   If a sequence is matched, the response `stop_reason` will be `stop_sequence`, and `stop_sequence` will contain the matched sequence.

### `stream`

*   **Type**: `boolean`
*   **Optional** (Defaults to `false`)
*   Whether to stream the response incrementally using server-sent events (SSE).
*   *See [streaming documentation](<link-to-streaming-docs>).* (Note: Replace `<link-to-streaming-docs>` with the actual URL)

### `system`

*   **Type**: `string`
*   **Optional**
*   A system prompt providing context, instructions, or persona guidance to Claude.
*   *See the guide on [system prompts](<link-to-system-prompts-guide>).* (Note: Replace `<link-to-system-prompts-guide>` with the actual URL)

### `temperature`

*   **Type**: `number`
*   **Optional** (Defaults to `1.0`)
*   Amount of randomness in the response. Ranges from `0.0` (more deterministic) to `1.0` (more creative).
*   Use lower values for analytical tasks, higher for creative tasks.
*   **Note**: Results are not fully deterministic even at `0.0`.
*   **Constraint**: `0.0 <= temperature <= 1.0`.

### `thinking` (Beta)

*   **Type**: `object`
*   **Optional**
*   Configuration object to enable Claude's "thinking" process visualization. Responses will include `thinking` content blocks before the final answer.
*   Requires a token budget and counts towards `max_tokens`.
*   *See [extended thinking documentation](<link-to-thinking-docs>).* (Note: Replace `<link-to-thinking-docs>` with the actual URL)

### `tool_choice`

*   **Type**: `object`
*   **Optional**
*   Specifies how the model should use the provided `tools`. Options generally include forcing a specific tool, allowing any tool, letting the model decide (`auto`), or disabling tool use. Refer to documentation for specific structures.

### `tools`

*   **Type**: `object[]`
*   **Optional**
*   An array defining tools the model can use. If tools are provided, the model might return `tool_use` content blocks. You can execute the tool and return results via `tool_result` content blocks.
*   **Each tool definition includes**:
    *   `name` (string): Name of the tool.
    *   `description` (string): Optional, but recommended description.
    *   `input_schema` (object): JSON Schema defining the tool's input parameters.
*   **Example Tool Definition**:
    ```json
    [
      {
        "name": "get_stock_price",
        "description": "Get the current stock price for a given ticker symbol.",
        "input_schema": {
          "type": "object",
          "properties": {
            "ticker": {
              "type": "string",
              "description": "The stock ticker symbol, e.g. AAPL for Apple Inc."
            }
          },
          "required": ["ticker"]
        }
      }
    ]
    ```
*   **Example `tool_use` block (in model response)**:
    ```json
    [
      {
        "type": "tool_use",
        "id": "toolu_01D7FLrfh4GYq7yT1ULFeyMV", // Unique ID for this tool use instance
        "name": "get_stock_price",
        "input": { "ticker": "^GSPC" }
      }
    ]
    ```
*   **Example `tool_result` message (to send back to model)**:
    ```json
    // Add this message object to the 'messages' array in your next request
    {
      "role": "user", // Tool results are provided under the 'user' role
      "content": [
        {
          "type": "tool_result",
          "tool_use_id": "toolu_01D7FLrfh4GYq7yT1ULFeyMV", // Matches the ID from tool_use block
          "content": "259.75 USD" // Can be a string
          // Or structured content: "content": [{"type": "text", "text": "259.75 USD"}]
          // Or indicate an error:
          // "is_error": true,
          // "content": "Tool execution failed: Ticker not found."
        }
      ]
    }
    ```
*   Tools enable workflows involving external functions or structured JSON output.
*   *See the [tools guide](<link-to-tools-guide>) for more details.* (Note: Replace `<link-to-tools-guide>` with the actual URL)

### `top_k`

*   **Type**: `integer`
*   **Optional**
*   Sample from only the top K most likely tokens at each step. Reduces less probable options.
*   Recommended for advanced use cases; `temperature` is usually sufficient.
*   **Constraint**: Must be greater than 0 (`> 0`).

### `top_p`

*   **Type**: `number`
*   **Optional**
*   Uses nucleus sampling. Samples from the smallest set of tokens whose cumulative probability exceeds `top_p`.
*   Alter either `temperature` or `top_p`, but typically not both.
*   Recommended for advanced use cases; `temperature` is usually sufficient.
*   **Constraint**: `0.0 < top_p <= 1.0`.

---

## Response (`200 OK application/json`)

### `content`

*   **Type**: `object[]`
*   **Required**
*   An array of content blocks generated by the model. Each block has a `type`.
*   Possible `type` values: `text`, `tool_use`, `thinking` (if requested).
*   **Example (Text Response)**:
    ```json
    [{"type": "text", "text": "Hi, I'm Claude."}]
    ```
*   If the request ended with an `assistant` message, `content` continues from that message.
*   **Example (Continuation)**:
    *   *Input `messages` ended with:* `{"role": "assistant", "content": "The best answer is ("}`
    *   *Possible `content` response:* `[{"type": "text", "text": "B)"}]`

### `id`

*   **Type**: `string`
*   **Required**
*   Unique identifier for the message object (e.g., `msg_013Zva2CMHLNnXjNJJKqJ2EF`). Format may change.

### `model`

*   **Type**: `string`
*   **Required**
*   The model that processed the request.

### `role`

*   **Type**: `string`
*   **Required**
*   The conversational role of the generated message.
*   **Value**: Always `assistant`.

### `stop_reason`

*   **Type**: `enum<string> | null`
*   **Required**
*   Reason the model stopped generating output. Possible values:
    *   `"end_turn"`: Natural stopping point reached.
    *   `"max_tokens"`: `max_tokens` limit hit or model's internal limit reached.
    *   `"stop_sequence"`: A custom `stop_sequences` was generated.
    *   `"tool_use"`: The model decided to use one or more tools.
*   **Note**: In non-streaming mode, this is always non-null. In streaming mode, it's `null` in the `message_start` event and non-null in subsequent events (like `message_delta`, `message_stop`).

### `stop_sequence`

*   **Type**: `string | null`
*   **Required**
*   If `stop_reason` is `stop_sequence`, this contains the actual stop sequence that was matched. Otherwise, it's `null`.

### `type`

*   **Type**: `string`
*   **Required**
*   The type of object.
*   **Value**: Always `message`.

### `usage`

*   **Type**: `object`
*   **Required**
*   Billing and rate-limit usage information based on token counts.
*   **Contains**:
    *   `input_tokens` (integer): Number of tokens in the processed input.
    *   `output_tokens` (integer): Number of tokens in the generated output.
*   **Note**: Token counts may not directly match the character count of input/output due to internal processing and encoding. `output_tokens` can be non-zero even for empty responses. Caching features (beta) may introduce additional usage fields like `cache_creation_input_tokens` and `cache_read_input_tokens`.
