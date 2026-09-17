# HTTP Web Requests, cURL Commands & API Pentesting Guide

## 1. The Foundation: URLs and Protocols

Almost all modern web and mobile applications communicate over the internet using HTTP (HyperText Transfer Protocol) or its secure counterpart, HTTPS.

### Anatomy of a URL

Resources are accessed via a URL containing specific components:

* **Scheme:** The protocol being used (e.g., `http://` or `https://`).

* **User Info:** Optional credentials for authentication (e.g., `admin:password@`).

* **Host:** The domain name or IP address of the target server.

* **Port:** The network port (defaults to `:80` for HTTP and `:443` for HTTPS).

* **Path:** The specific file or directory being requested (e.g., `/dashboard.php`).

* **Query String:** Parameters passed to the server, starting with `?` (e.g., `?login=true`).

* **Fragment:** Client-side markers processed by the browser, starting with `#`.

### HTTP vs. HTTPS

* **HTTP:** Transmits data in clear-text, making it highly vulnerable to Man-in-the-Middle (MitM) attacks where attackers can intercept credentials in plain text.

* **HTTPS:** Encrypts the entire communication stream using a TLS key exchange, protecting sensitive data across the wire.

## 2. HTTP Methods & Status Codes

HTTP methods instruct the server on what action to perform, and status codes indicate the result of that action.

### Common Methods

* **GET:** Requests a resource. Data is passed in the URL (query string).

* **POST:** Sends data to the server in the request body. Ideal for sending large amounts of data, file uploads, and preventing parameters from being logged in server access logs.

* **PUT / PATCH:** Updates an existing resource (PUT replaces the entry, PATCH updates specific fields).

* **DELETE:** Removes a resource from the server.

* **OPTIONS:** Asks the server what HTTP methods are supported/allowed on an endpoint.

### Status Code Classes

* **1xx (Informational):** Request received, continuing process.

* **2xx (Success):** E.g., `200 OK` (Request succeeded).

* **3xx (Redirection):** E.g., `301 Moved Permanently` or `302 Found` (Redirects client to a new URL).

* **4xx (Client Error):** E.g., `400 Bad Request`, `403 Forbidden` (Access denied), `404 Not Found`.

* **5xx (Server Error):** E.g., `500 Internal Server Error` (Backend execution failure).

## 3. The Pentester's Toolkit: Essential cURL Commands & DevTools

Web browsers render visual code (HTML/CSS/JS), but penetration testers need to interact directly with raw request and response data.

### Browser DevTools (F12)

* Use the **Network Tab** to monitor background requests sent by the browser.

* **Pro Tip:** Right-click any request in the Network Tab and select **Copy > Copy as cURL** to instantly replicate a complex browser request inside your terminal.

### Essential cURL Commands & Examples

#### 1. Basic Request (`GET`)

* **Command:** `curl http://example.com`

* **Why:** Fetches and prints the raw HTML/text content of a webpage to your terminal.

* **Easy Example:** Imagine asking a website "Show me your homepage code," and it dumps all the raw HTML onto your screen.

#### 2. Verbose Mode (`-v`)

* **Command:** `curl -v http://example.com`

* **Why:** Displays the exact request headers you sent and the exact response headers the server sent back.

* **Easy Example:** Turn on "x-ray vision" to see the hidden conversation (status codes, server type, date) happening behind the scenes.

#### 3. Fetch Headers Only (`-I`)

* **Command:** `curl -I http://example.com`

* **Why:** Sends a `HEAD` request to retrieve only the response headers without downloading the webpage body.

* **Easy Example:** Read the label on a box (checking server type, cookies, or status code) without opening and dumping out what's inside.

#### 4. Change HTTP Method (`-X`)

* **Command:** `curl -X POST http://example.com`

* **Why:** Forces `cURL` to use a specific HTTP action (like `POST`, `PUT`, `DELETE`, or `OPTIONS`) instead of the default `GET`.

* **Easy Example:** Switch from asking to *read* a page (`GET`) to asking to *submit* data (`POST`) or *remove* something (`DELETE`).

#### 5. Send Request Body Data (`-d`)

* **Command:** `curl -X POST -d 'username=admin&password=123' http://example.com/login`

* **Why:** Sends form parameters or body data inside a `POST` or `PUT` request.

* **Easy Example:** Fill out a login form with a username and password, then press the "Submit" button.

#### 6. Add Custom Headers (`-H`)

* **Command:** `curl -H 'Content-Type: application/json' http://example.com/api`

* **Why:** Injects extra information into your request so the server knows how to process it.

* **Easy Example:** Attach a sticky note to your request telling the backend, "Hey, I'm sending you JSON data, so parse it accordingly."

#### 7. Pass Cookies (`-b`)

* **Command:** `curl -b 'PHPSESSID=abc123xyz' http://example.com/dashboard`

* **Why:** Sends a session cookie along with your request to access pages that require you to be logged in.

* **Easy Example:** Flash your VIP wristband at the door so you can walk straight into the `/dashboard` without typing your password again.

#### 8. HTTP Basic Authentication (`-u`)

* **Command:** `curl -u admin:password123 http://example.com/admin`

* **Why:** Sends `username:password` credentials automatically encoded into a Base64 `Authorization` header.

* **Easy Example:** Automatically answer a pop-up prompt asking for a username and password before loading a protected folder.

#### 9. Ignore SSL/TLS Warnings (`-k`)

* **Command:** `curl -k https://127.0.0.1:8080`

* **Why:** Tells `cURL` to ignore untrusted or self-signed SSL/TLS certificates.

* **Easy Example:** Bypass the "Your connection is not private" warning screen when testing a local lab or proxy tool like Burp Suite.

#### 10. Follow Redirects (`-L`)

* **Command:** `curl -L http://example.com`

* **Why:** Automatically follows `301` or `302` redirects until it reaches the final destination page.

* **Easy Example:** If a site moves from `http://` to `https://`, this command automatically follows the signpost to the new address instead of stopping.

#### 11. Save Output to a File (`-o` / `-O`)

* **Command:** `curl -o page.html http://example.com`

* **Why:** Saves the response into a specified file (`-o filename`) or keeps the remote file name (`-O`) instead of printing it to your terminal.

* **Easy Example:** Download a file or save a webpage directly to your folder like clicking "Save As...".

#### 12. Silent Mode (`-s`)

* **Command:** `curl -s http://example.com`

* **Why:** Hides progress bars and error messages.

* **Easy Example:** Keep your terminal clean when sending the output directly to another command (like formatting JSON with `curl -s ... | jq`).

## 4. Headers & State Management

Because HTTP is stateless (it forgets user sessions between requests), applications use headers and cookies to track user state.

* **Authorization:** Used for Basic Auth (Base64 encoded credentials like `Basic YWRtaW46YWRtaW4=`).

* **Cookie / Set-Cookie:** The server issues a token via `Set-Cookie`, and the browser returns it via `Cookie` on subsequent requests to stay logged in.

* **Host:** Tells the server which specific website you are requesting, as one IP address can host multiple virtual domains.

* **Security Headers:** Instruct the browser on security rules, such as `Content-Security-Policy` (restricts external scripts to prevent XSS) and `Strict-Transport-Security` (forces HTTPS connections).

## 5. API CRUD Operations

Modern REST APIs map standard database operations directly to HTTP methods:

| Operation | HTTP Method | API Example Command | 
 | ----- | ----- | ----- | 
| **C**reate | **POST** | `curl -X POST http://target/api/city/ -d '{"city_name":"Cairo"}' -H 'Content-Type: application/json'` | 
| **R**ead | **GET** | `curl -s http://target/api/city/Cairo | jq` | 
| **U**pdate | **PUT** | `curl -X PUT http://target/api/city/Cairo -d '{"city_name":"New_Cairo"}' -H 'Content-Type: application/json'` | 
| **D**elete | **DELETE** | `curl -X DELETE http://target/api/city/New_Cairo` | 

## 6. Real-World Bug Bounty Additions

### Intercepting cURL with Burp Suite

While `curl` is fantastic for automation, finding complex logic flaws requires Burp Suite. You can pipe any `curl` command directly into Burp Suite's proxy by adding the `-x` flag. This allows you to catch the command in Burp, send it to the Repeater, and manually manipulate the payloads.

* **Command:** `curl -x http://127.0.0.1:8080 -k -X POST -d '{"search":"admin"}' http://target.com/api`

* *Note: The `-k` flag is crucial here because Burp Suite intercepts traffic using its own self-signed TLS certificate.*

### Host Header Injection

The `Host` header is a massive target in bug bounty hunting. Because developers often use the `Host` header to dynamically generate links (like password reset URLs), manipulating it can lead to critical vulnerabilities.

* **The Attack:** Change `Host: target.com` to `Host: evil.com`. If you trigger a password reset, the server might email the victim a reset link pointing to `http://evil.com/reset?token=123`. When they click it, you steal their token.

### Modern API Authentication (JWT)

While Basic Auth is heavily featured in older applications, modern APIs almost exclusively use JSON Web Tokens (JWTs).

* Instead of `Authorization: Basic [base64_creds]`, you will see `Authorization: Bearer [ey...]`.

* JWTs are stateless. If you spot one in an API request, grab it and decode it (using a tool like `jwt.io`) to see if you can manipulate the payload (e.g., changing `"role": "user"` to `"role": "admin"`) and bypass signature verification.