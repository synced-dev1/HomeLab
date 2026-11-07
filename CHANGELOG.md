# Homelab Changelog

## Version 1: Registration and Database (Api)
- Added user registration backed by SQLite.
- Added the initial database schema and query helpers.

## Version 2: Authentication and Sessions (Api)
- Added login authentication.
- Added session cookies and session expiration cleanup.
- Protected authenticated API routes with session middleware.

## Version 3: Chunked File Uploads and Configuration (Api)
- Added an authenticated `/upload` endpoint for chunked uploads.
- Added chunk assembly and file system-backed storage under the configured storage path. This is local/file system storage, not an external cloud storage service.
- Added `DB_PATH` and `STORAGE_PATH` environment variable configuration.
- Added a folders table and basic create/get database queries. Folder records are not yet connected to the upload flow, and uploaded files do not yet have database records.
- Added periodic cleanup of expired sessions from the in-memory session store.
- Added `README.md` and `CHANGELOG.md`.

## Version 4: Cleanup And Documentation
- Initialized Git in the parent directory of `/api` (HomeLab); the API remains a separate Git repository tracked as a submodule.
- Added some folders/files to `.gitignore` in parent directory
- Moved `CHANGELOG.md` and `README.md` from `/api` to parent folder
- Added comments to every singular coding file in `/api`
- Renamed `api/storage/files.go` to `api/storage/uploads.go`
- Renamed `CHANGELOG.MD` to the more appropriate name `CHANGELOG.md`
- Renamed function in `api/db/queries.go`
- Updated `README.md` with the current local filesystem upload behavior, deployment directory, and `/upload` request details and endpoint table.
- Fixed Markdown Files syntax and cleaned them up
- Removed endpoint api handler `/api` for memory optimization due to not providing any value
- Removed endpoint `/me` due to security risks leaking sensitive information in the response body
- Updated `CHANGELOG.md` and added some missing info from previous versions
- Minor code fixes
- Created video for `README.md` in parent directory called `V4_README_Video.mp4`
