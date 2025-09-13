
# 🔑 File Hash Analyzer API

The File Hash Analyzer API provides a lightweight and fast way to check if a file hash (MD5 or SHA256) is malicious, safe, or unknown.
It integrates MalwareBazaar intelligence feeds and allows crowdsourced reporting to keep the database growing.
Perfect for SIEM tools, SOC dashboards, malware sandboxes, and email gateways.

![File Hash Analyzer API](https://res.cloudinary.com/ds64xs2lp/image/upload/v1757800391/file_hash_analyzer_api_gwciwc.gif)

* [Get Started on RapidAPI](https://rapidapi.com/dakidarts-dakidarts-default/api/file-hash-analyzer-api)
* [API Docs on Dakidarts](https://dakidarts.com/api/file-hash-analyzer-api/)

**Base URL:**

```
https://file-hash-analyzer-api.p.rapidapi.com
```

**Authentication:**

* `x-rapidapi-key`: Your RapidAPI key
* `x-rapidapi-host`: `https://file-hash-analyzer-api.p.rapidapi.com`

---

## 1️⃣ `/analyze` — Analyze a hash

**Method:** `GET` or `POST`
**Description:** Query the database for a hash to check if it’s known malicious, safe, or unknown.

**Request Parameters (GET)**

| Parameter | Type   | Required | Description                                 |
| --------- | ------ | -------- | ------------------------------------------- |
| hash      | string | Yes      | Hash to check (MD5 or SHA256)               |
| algorithm | string | No       | `md5` or `sha256`. Auto-detected if omitted |

**Request Body (POST)**

```json
{
  "hash": "e99a18c428cb38d5f260853678922e03",
  "algorithm": "md5" 
}
```

**Response (200 OK)**

```json
{
  "hash": "e99a18c428cb38d5f260853678922e03",
  "algorithm": "md5",
  "verdict": "malicious",
  "tags": ["agenttesla"],
  "source": "MalwareBazaar-AgentTesla",
  "votes": 3
}
```

**Response (404 Not Found)**

```json
{
  "hash": "unknownhash",
  "algorithm": "sha256",
  "verdict": "unknown",
  "tags": [],
  "source": null
}
```

**Errors**

* `400` → Invalid hash or unsupported algorithm
* `500` → Internal server error

---

## 2️⃣ `/report` — Report a hash

**Method:** `POST`
**Description:** Crowdsource a hash, adding a new entry or updating an existing one.

**Request Body**

```json
{
  "hash": "e99a18c428cb38d5f260853678922e03",
  "algorithm": "md5",
  "verdict": "malicious",
  "tags": ["agenttesla", "keylogger"],
  "source": "user-report"
}
```

**Response Examples**

* **201 Created** (new entry added)

```json
{
  "status": "created",
  "entry": { ...HashEntry object... }
}
```

* **200 OK** (existing entry updated)

```json
{
  "status": "updated",
  "entry": { ...HashEntry object... }
}
```

* **200 OK** (merge votes due to integrity error)

```json
{
  "status": "merged",
  "entry": { ...HashEntry object... }
}
```

**Errors**

* `400` → Unsupported or undetectable hash
* `500` → Internal server error


---

## 💡 Notes

* Supported hash algorithms: `md5`, `sha256`.
* Tags are lowercase, comma-separated strings (e.g., `"agenttesla,keylogger"`).

---

## 🔗 Example `curl` requests

**Analyze hash (GET)**

```bash
curl -X GET "https://file-hash-analyzer-api.p.rapidapi.com/analyze?hash=&lt;md5_or_sha256&gt;" \
-H "x-rapidapi-key: YOUR_RAPIDAPI_KEY"
```

**Analyze hash (POST)**

```bash
curl -X POST "https://file-hash-analyzer-api.p.rapidapi.com/analyze" \
-H "x-rapidapi-key: YOUR_RAPIDAPI_KEY" \
-H "Content-Type: application/json" \
-d '{"hash":"&lt;hash&gt;", "algorithm":"md5"}'
```

**Report a hash**

```bash
curl -X POST "https://file-hash-analyzer-api.p.rapidapi.com/report" \
-H "x-rapidapi-key: YOUR_RAPIDAPI_KEY" \
-H "Content-Type: application/json" \
-d '{"hash":"&lt;hash&gt;","verdict":"malicious","tags":["agenttesla"]}'
```

