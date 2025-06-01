# Economy Simulator Setup Guide

This guide will walk you through setting up Economy Simulator (a 2016-style Roblox clone).

---

## ✅ Requirements

* Windows Server 2022 or 2025
* Hyper-V enabled

---

## 1. Install Ubuntu & Redis (via WSL)

1. Open PowerShell as Administrator and run:

   ```bash
   wsl --install
   ```

   > If errors occur, enable the following in "Windows Features":
   >
   > * Windows Subsystem for Linux
   > * Windows Virtual Machine Platform

2. Restart your PC. Ubuntu should open automatically.

3. Set up a UNIX username (e.g., `Admin`) and password.

4. Run:

   ```bash
   sudo apt update
   sudo apt install redis
   redis-server
   ```

   > Keep the Redis server running in this terminal.

---

## 2. Download Required Apps

| App         | Version / Link                                                                                             |
| ----------- | ---------------------------------------------------------------------------------------------------------- |
| Node.js     | [v18.16.1](https://nodejs.org/dist/v18.16.1/node-v18.16.1-x64.msi)                                         |
| PostgreSQL  | [Download](https://sbp.enterprisedb.com/getfile.jsp?fileid=1258627)                                        |
| .NET 6 SDK  | [Download](https://dotnet.microsoft.com/en-us/download/dotnet/thank-you/sdk-6.0.412-windows-x64-installer) |
| Go (Golang) | [v1.20.6](https://go.dev/dl/go1.20.6.windows-amd64.msi)                                                    |

Install in this order:

1. PostgreSQL (set a memorable password)
2. Node.js
3. .NET 6
4. Golang

---

## 3. Initial Configuration

### PostgreSQL Connection

In `services/api/config.json`, paste:

```json
{
  "knex": {
    "client": "pg",
    "connection": {
      "host": "127.0.0.1",
      "user": "postgres",
      "password": "YOUR_PASSWORD_HERE",
      "database": "YOUR_DATABASE_NAME_HERE"
    }
  }
}
```

Run:

```bash
cd services/api
npm i
npx knex migrate:latest
```

---

## 4. Website Setup

1. Go to `services/Roblox/Roblox.Website`

2. Rename `appsettings.example.json` to `appsettings.json`

3. Edit:

   * Line 37: `"OwnerUserId": "1"`
   * Line 26:

     ```json
     "Postgres": "Host=127.0.0.1; Database=postgres; Password=password; Username=postgres; Maximum Pool Size=20",
     ```

4. Press `Ctrl + H` and replace:

   ```
   C:\\Users\\mark\\Desktop\\
   ```

   with:

   ```
   C:\\Users\\<YourUsername>\\Downloads\\
   ```

   > Make sure the folder is named `ECS` and is in Downloads.

5. Folder setup:

```
services/api/storage/asset
services/api/public/images/thumbnails
services/api/public/images/group
```

6. Run:

```bash
cd services/Roblox/Roblox.Website
 dotnet run
```

---

## 5. Admin Panel Setup

```bash
cd services/admin
npm i
npm run build
```

---

## 6. Frontend Setup

1. Go to `services/2016-roblox-main`
2. Create `config.json`:

```json
{
  "serverRuntimeConfig": {
    "backend": {
      "csrfKey": "g0qiiDZw7jM2l54+7qsuRaymx6nBGdCKT9Kc0bqJB3aZ26rSsPMXfg8uWfUBtTqWenDVy+AQS1jkdrgvUwVSsw=="
    }
  },
  "publicRuntimeConfig": {
    "backend": {
      "proxyEnabled": true,
      "flags": {
        "myAccountPage2016Enabled": true,
        "catalogGenreFilterSupported": true,
        "catalogPageLimit": 28,
        "catalogSaleCountVisibleFromDetailsEndpoint": true,
        "commentsEndpointHasAreCommentsDisabledProp": true,
        "catalogDetailsPageResellerLimit": 10,
        "avatarPageInventoryLimit": 10,
        "friendsPageLimit": 25,
        "settingsPageThemeSelectorEnabled": true,
        "tradeWindowInventoryCollectibleLimit": 10,
        "moneyPagePromotionTabVisible": true,
        "gameGenreFilterSupported": true,
        "avatarPageOutfitCreatedAtAvailable": true,
        "catalogDetailsPageOwnersTabEnabled": true,
        "launchUsingEsURI": true
      },
      "baseUrl": "https://your.domain/",
      "apiFormat": "https://your.domain/apisite/{0}{1}"
    }
  }
}
```

Replace `your.domain` with your actual domain.

Run:

```bash
npm i
npm run build
npm run start
```

---

## 7. Asset Validation & Game Server

### Asset Validation:

```bash
cd services/AssetValidationServiceV2
go run main.go
```

### RCCService:

```bash
cd services/RCCService
RCCService.exe -console -placeid:1818
```

### Game Server Setup:

1. `services/game-server/config.json`:

```json
{
  "rcc": "C:\\Users\\<YourUsername>\\Downloads\\ECS\\services\\RCCService",
  "authorization": "90WGEGNJGWHIWGOI31900H9GIOGI",
  "baseUrl": "http://localhost:5000",
  "rccPort": 64989,
  "port": 3040,
  "websiteBotAuth": "UW8U8TU9W9R8RHGRJOGWGOINOOWGNWRNJWWNRJ",
  "thumbnailWebsocketPort": 3189,
  "dockerDisabled": true
}
```

2. Replace `economy-simulator.org` with your domain in all files inside:

   * `services/game-server/scripts/gameserver.lua`
   * `scripts/player/*`
   * `scripts/asset/*`

3. Run:

```bash
npm i
npm run build
npm run start
```

---

## 8. Cloudflare Tunnel Setup

1. Register at [Cloudflare](https://cloudflare.com), add your domain
2. Go to: **Traffic > Cloudflare Tunnel** > Launch Zero Trust Dashboard
3. Go to **Access > Tunnels**, create one
4. Download Cloudflared: [Download](https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-windows-amd64.msi)
5. Run PowerShell as admin and execute the command shown on Cloudflare after tunnel creation
6. Setup hostname:

   * Type: `HTTP`
   * URL: `localhost:5000`
   * Domain: your custom domain

---

## 9. Final Setup

1. Go to `services` folder
2. Run:

```bash
runall.bat
```

3. Go to your site and register an account. This will be ID 1 (admin).

4. Go to `https://your.domain/admin`, create a new player:

   * ID: 2
   * Username: UGC
   * Password: anything
   * Then nullify the password for safety.

5. Create another user:

   * ID: 12
   * Username: BadDecisions
   * Use a secure password.

---

## ✅ Done!

You're now running your own 2016-style Roblox site!
