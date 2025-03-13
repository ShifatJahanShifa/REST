# HTTP Caching Demo with Express.js

This project demonstrates how to implement HTTP caching in a Node.js application using Express. It covers caching for static assets (CSS, JS, images) and API responses, along with the use of Cache-Control, ETag, and If-None-Match headers.

## Features

- **Static Asset Caching**: Cache static files (e.g., CSS, JS, images) for 1 hour using `Cache-Control: max-age=3600`.
- **API Response Caching**: Cache API responses for 1 minute using `Cache-Control: public, max-age=60`.
- **ETag Validation**: Use ETag and If-None-Match headers for cache validation.
- **Simple Frontend**: A basic HTML page to demonstrate caching behavior.

## Prerequisites

- Node.js (v14 or higher)
- npm (Node Package Manager)

## Setup

1. Clone the repository:
```bash
git clone <repository-url>
cd <repository-folder>
```

2. Install dependencies:
```bash
npm install express
```

3. Run the server:
```bash
node app.js
```

4. Open the application in your browser:
   - Visit http://localhost:5000.

## Project Structure

```
.
├── app.js                # Express server implementation
├── public/               # Static assets (CSS, JS, images)
│   └── style.css         # Example CSS file
├── index.html            # Simple HTML frontend
└── README.md             # This file
```

## Code Overview

### 1. Static Asset Caching

Static assets (e.g., CSS, JS, images) are served with a Cache-Control header set to max-age=3600 (1 hour). This ensures that the browser caches these files and reuses them for subsequent requests within the cache duration.

```javascript
app.use("/static", express.static(path.join(__dirname, "public"), {
  maxAge: "1h", // Cache static assets for 1 hour
}));
```

### 2. API Response Caching

API responses are cached for 1 minute using `Cache-Control: public, max-age=60`. The ETag header is automatically generated by Express for cache validation.

```javascript
app.get("/api/data", (req, res) => {
  res.set("Cache-Control", "public, max-age=60"); // Cache API response for 1 minute
  res.json({ message: "This is cached data!", timestamp: new Date() });
});
```

### 3. ETag Validation

Express automatically generates an ETag for responses. When the browser makes a subsequent request, it includes the ETag in the If-None-Match header. If the resource hasn't changed, the server responds with 304 Not Modified.

### 4. Frontend

The index.html file demonstrates how to load static assets and fetch data from the API.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Simple Caching Demo</title>
  <link rel="stylesheet" href="/static/style.css">
</head>
<body>
  <h1>Welcome to the Simple Caching Demo!</h1>
  <p>This page demonstrates HTTP caching for static assets and API responses.</p>
  <button id="fetchData">Fetch Data</button>
  <pre id="output"></pre>

  <script>
    document.getElementById("fetchData").addEventListener("click", async () => {
      const response = await fetch("/api/data");
      const data = await response.json();
      document.getElementById("output").textContent = JSON.stringify(data, null, 2);
    });
  </script>
</body>
</html>
```

## Testing Caching Behavior

### Static Assets:
1. Open the browser's developer tools (F12) and go to the Network tab.
2. Load http://localhost:5000/static/style.css.
3. Observe the `Cache-Control: max-age=3600` header in the response.
4. Reload the page and notice that the file is served from the cache.

### API Responses:
1. Click the "Fetch Data" button on the page.
2. Observe the `Cache-Control: public, max-age=60` header in the response.
3. Click the button multiple times within 1 minute. The response will be served from the cache.
4. After 1 minute, the cache expires, and a new request is made to the server.

### ETag Validation:
1. Modify the API response data in app.js.
2. Reload the page and fetch the data again. The ETag will change, and the browser will receive the updated response.

