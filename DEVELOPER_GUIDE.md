# HomeLab Developer Guide

This guide covers the current API and Linux deployment details. For a plain-language project overview, see [README.md](README.md).

*Running the executable with `DEBUG=1` will turn on debug mode*
## Current API

| Method | Path        | Authentication | Purpose                               |
| ------ | ----------- | -------------- | ------------------------------------- |
| GET    | `/`         | No             | Return the server welcome message     |
| POST   | `/register` | No             | Create an account                     |
| POST   | `/login`    | No             | Authenticate and set a session cookie |
| POST   | `/upload`   | Yes            | Upload chunks and assemble a file     |

`/upload` takes a session cookie and these headers. The request body contains the raw bytes of one chunk.

| Header | Purpose |
| --- | --- |
| `X-Filename` | Completed file name |
| `X-Target-Path` | Directory within the user's storage directory |
| `X-Chunk-Index` | 1-based index of this chunk |
| `X-Total-Chunks` | Number of chunks in the upload |

Example registration request:

```http
POST /register HTTP/1.1
Host: localhost:8080
Content-Type: application/json

{"Username":"test-user","Password":"test-password","Email":"test@example.com"}
```

Example login request:

```http
POST /login HTTP/1.1
Host: localhost:8080
Content-Type: application/json

{"Username":"test-user","Password":"test-password"}
```

Example first upload chunk (repeat for each chunk, changing the index and body):

```http
POST /upload HTTP/1.1
Host: localhost:8080
Cookie: session=SESSION_COOKIE_FROM_LOGIN
X-Filename: archive.zip
X-Target-Path: backups
X-Chunk-Index: 1
X-Total-Chunks: 2
Content-Type: application/octet-stream

<raw chunk bytes>
```

