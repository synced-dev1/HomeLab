# HomeLab

HomeLab is a self-hosted, API first, file service for a home network. The release will run as a prebuilt executable, so users can start the service without working with the source code.

## Current component: API

The API is the part available as an executable today. It can:

- Register accounts and sign users in.
- Accept authenticated, chunked file uploads.
- Store uploaded files on the host computer and record file and folder details, including file size.

The API has authenticated upload and download endpoints. A complete file-browsing website is not available yet, so clients need to communicate with the API directly.

## Planned component: Website

A website for browsing and downloading stored files is planned. It is not available yet.

## Deployment guide

The API listens on port `8080`. It needs two writable directories: `DB_PATH` for `homelab.db` and `STORAGE_PATH` for uploaded files. Keep these directories on persistent storage and back them up. Download the executable for your operating system and CPU from GitHub Releases using your browser. The commands below assume the file is in your Downloads folders.

The server listens on all network interfaces. Use it on a trusted network, and allow port `8080` through the host firewall only for devices that should reach it. Do not expose it directly to the public internet.

### Windows (PowerShell, x64)

Set the data folders permanently for your Windows user and create them:

```powershell
$dbPath = "$env:LOCALAPPDATA\HomeLab\db"
$storagePath = "$env:LOCALAPPDATA\HomeLab\files"
[Environment]::SetEnvironmentVariable("DB_PATH", $dbPath, "User")
[Environment]::SetEnvironmentVariable("STORAGE_PATH", $storagePath, "User")
New-Item -ItemType Directory -Force $dbPath, $storagePath | Out-Null
```

Close and reopen PowerShell so it loads the saved variables, then run the downloaded executable:

```powershell
& "$HOME\Downloads\homelab-api-windows-amd64.exe"
```

### Linux (x64)

Save the variables in your Bash profile, create the data folders, grant the downloaded executable permission to run, and start it:

```bash
printf '%s\n' 'export DB_PATH="$HOME/.local/share/homelab/db"' 'export STORAGE_PATH="$HOME/.local/share/homelab/files"' >> "$HOME/.bashrc"
source "$HOME/.bashrc"
mkdir -p "$DB_PATH" "$STORAGE_PATH"
chmod +x "$HOME/Downloads/homelab-api-linux-amd64"
"$HOME/Downloads/homelab-api-linux-amd64"
```

For a 64-bit ARM Linux host, use `homelab-api-linux-arm64` in the last two commands.

### macOS (Apple silicon)

Save the variables in your Zsh profile, create the data folders, grant the downloaded executable permission to run, and start it:

```bash
printf '%s\n' 'export DB_PATH="$HOME/Library/Application Support/HomeLab/db"' 'export STORAGE_PATH="$HOME/Library/Application Support/HomeLab/files"' >> "$HOME/.zshrc"
source "$HOME/.zshrc"
mkdir -p "$DB_PATH" "$STORAGE_PATH"
chmod +x "$HOME/Downloads/homelab-api-darwin-arm64"
"$HOME/Downloads/homelab-api-darwin-arm64"
```

For an Intel Mac, use `homelab-api-darwin-amd64` in the last two commands.

Keep the server running while using the API. The first start creates the database schema in `DB_PATH`. Stop it with `Ctrl+C`; the database and uploaded files remain in their configured directories.

## Current limitations

- No complete file-browsing website yet.
- The upload request limit is 500 MiB per chunk; there is no total-size limit across all chunks yet.
- The API is an early prototype for a trusted home network.
