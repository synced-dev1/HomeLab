# Custom Homelab

***A lightweight, high performance, Go backend powering some self-hosted tools***

## Features
-  **Current**:
	- Authenticated chunked uploads to local filesystem storage
- **Future**:
	- PiDrop (like Airdrop but cross-platform)
	- Media Streamer (like Netflix but self-hosted)
	- Password Vault/Manager
	- Ad Blocker

## Functionality

### Current Functionality
The app supports user registration, login with an HTTP-only session cookie, and authenticated chunked uploads. Uploaded chunks are assembled into files on the server's local filesystem under `STORAGE_PATH`. Uploads are not currently recorded in the database, and there is no download endpoint.
### Future Functionality
In the future it should have the rest of the features listed above plus redownloading the file from storage and test files that will have tutorials on how to be used.

## Current Tests
- Manually tested with a 1 GiB chunk. This is not a configured or verified maximum; the server currently has no explicit upload-size limit.
## Known Limitations
Uploads are not recorded in the database yet.

## Endpoint Testing And Call Reference

| Method | Path        | Auth Required | Purpose                                       |
| ------ | ----------- | ------------- | --------------------------------------------- |
| GET    | `/`         | No            | Return the server welcome message             |
| POST   | `/register` | No            | Create an account                             |
| POST   | `/login`    | No            | Authenticate and set the session cookie       |
| POST   | `/upload`   | Yes           | Upload chunks and assemble the completed file |

`/upload` requires the session cookie set by `/login`, these headers, and the raw bytes for one chunk in the request body:

| Header           | Description                                  |
| ---------------- | -------------------------------------------- |
| `X-Filename`     | Name of the completed file                   |
| `X-Target-Path`  | Directory below the user's storage directory |
| `X-Chunk-Index`  | 1-based index of this chunk                  |
| `X-Total-Chunks` | Total number of chunks in the upload         |

The server assembles the file when it receives the final indexed chunk and all expected chunk files are present.

Example (repeat once per chunk, changing the index and request body):

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
#### Register Endpoint `/register`
```Http Request
POST /register HTTP/1.1
Host: localhost:8080
Content-Type: application/json

{"Username":"test-user","Password":"test-password","Email":"test@example.com"}
```

#### Login Endpoint `/login`
```HTTP Request
POST /login HTTP/1.1
Host: localhost:8080
Content-Type: application/json

{"Username":"test-user","Password":"test-password"}
```

## Deployment Guide
### Note this guide is for Linux only

Navigate to the API module directory:

```bash
cd ~/HomeLab/api
```

Then once you are in the API directory you have to compile the app for your target architecture

```bash
#For Raspberry Pi Or cross-compilation
GOOS=linux GOARCH=arm64 go build -o server .
```

`GOOS` means go OS, which is what OS you are compiling for, and `GOARCH` means what architecture you are compiling for.

Next you must set your environment variable.

```bash
#For Deployment on Raspberry Pi or other devices (Permanent Environment Variable)
nano ~/.bashrc
```

Once in ``` .bashrc ``` copy and paste this at the end of the file

```bash
export STORAGE_PATH="YOUR-SSD-PATH"
# DB Path is where the code automatically makes the db 
export DB_PATH="YOUR-DB-PATH"
```

Then hit ``` O^ ``` then hit ``` Enter ``` and finally to save your environment variable.

