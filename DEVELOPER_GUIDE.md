# HomeLab Developer Guide

This guide documents the current API. For installation and deployment instructions, see [README.md](README.md).

Set `DEBUG=1` to print debug logs.

## Current API

| Method | Path        | Authentication | Purpose                                      |
| ------ | ----------- | -------------- | -------------------------------------------- |
| GET    | `/`         | No             | Return the server welcome message            |
| POST   | `/register` | No             | Create an account                            |
| POST   | `/login`    | No             | Authenticate and set a session cookie        |
| POST   | `/upload`   | Yes            | Upload a chunk; assemble the file at the end |
| GET    | `/download` | Yes            | Download a file, optionally by byte range    |

Sessions are sent in the `session` cookie returned by `/login` and expire after one hour.

`/upload` takes a session cookie and these headers. Its request body is the raw bytes of one chunk. A blank `X-Target-Path` stores the file at the user's storage root. Each chunk is limited to 500 MiB; this is a per-chunk limit, not a total file-size limit.

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

`/download` expects a JSON body with the relative folder path and filename. An empty `Path` selects the user's storage root. The server streams the file and supports standard HTTP `Range` requests for resumed downloads; a partial response uses status `206` and includes `Content-Range`.

Example full download request:

```http
GET /download HTTP/1.1
Host: localhost:8080
Cookie: session=SESSION_COOKIE_FROM_LOGIN
Content-Type: application/json

{"Path":"backups","Filename":"archive.zip"}
```

Example request for a byte range:

```http
GET /download HTTP/1.1
Host: localhost:8080
Cookie: session=SESSION_COOKIE_FROM_LOGIN
Range: bytes=0-1048575
Content-Type: application/json

{"Path":"backups","Filename":"archive.zip"}
```

