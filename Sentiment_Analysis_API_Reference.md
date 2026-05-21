# API Reference: Sentiment Prediction Endpoint

This documentation provides the technical specification for the sentiment analysis prediction endpoint. Backend engineering teams can use this API to integrate real-time user feedback into customer-facing dashboards.

## Endpoint Overview

The `predict` endpoint accepts raw text inputs and returns a binary sentiment classification along with detailed model confidence metrics. The API supports English, Spanish, and French text processing with a hard restriction on input length to optimize model performance.

| Method | Path | Content-Type | Accept |
| :--- | :--- | :--- | :--- |
| `POST` | `/predict` | `application/json` | `application/json` |

## Request Body Schema

The request payload must be a JSON object containing the text to be analyzed and an optional language configuration.

| Field | Type | Required | Description | Constraints |
| :--- | :--- | :--- | :--- | :--- |
| `text` | `string` | **Yes** | The raw text sequence to be processed by the sentiment analysis model. | Maximum length: 512 characters. Cannot be empty. |
| `language` | `string` | No | The language code of the input text. Defaults to `"en"`. | Accepted values: `"en"`, `"es"`, `"fr"`. |

## Response Body Schema (200 OK)

Upon a successful inference request, the model returns a JSON payload containing the final classification, confidence scores, and system metadata.

| Field | Type | Description |
| :--- | :--- | :--- |
| `label` | `string` | The predicted sentiment classification for the text. Returns either `"positive"` or `"negative"`. |
| `score` | `float` | The confidence probability score (ranging from `0.0` to `1.0`) specifically for the predicted `label`. |
| `confidence` | `array of floats` | A raw probability distribution array structured exactly as `[negative_prob, positive_prob]`. |
| `model_version` | `string` | The semantic versioning identifier of the active model executing the inference (e.g., `"v1.4.2"`). |

## Request & Response Examples

### Example Payload
This example demonstrates a standard English text submission with default language settings.

**Sample Request:**
```json
{
  "text": "I absolutely loved this product!"
}

**Sample Response:**
```json
{
  "label": "positive",
  "score": 0.94,
  "confidence": [
    0.06,
    0.94
  ],
  "model_version": "v1.4.2"
}

## Error Handling

The API uses standard HTTP status codes to indicate the success or failure of an API request. Review the table below for error descriptions and required client-side resolution steps.

| Status Code | Meaning | Cause / Description | Actionable Developer Instruction |
| :--- | :--- | :--- | :--- |
| `400` | Bad Request | Missing required text field. | Ensure the request body contains a valid, non-empty `"text"` key before retrying. |
| `413` | Payload Too Large | Input exceeds 512 characters. | Implement client-side string truncation or character counting to enforce the 512-character limit before transmission. |
| `429` | Too Many Requests | Rate limit exceeded. | Decrease request velocity. Implement an exponential backoff retry mechanism in your integration script. |
| `500` | Internal Server Error | Internal model error. | The model worker encountered an unexpected execution error. Log the incident and retry with a fresh connection. Contact DevOps if persistence occurs. |
